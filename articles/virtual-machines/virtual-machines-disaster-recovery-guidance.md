---
title: Scénáře zotavení po havárii pro virtuální počítače Azure | Microsoft Docs
description: Zjistěte, co dělat v případě, že přerušení služby Azure má dopad na virtuálních počítačích Azure.
services: virtual-machines
documentationcenter: ''
author: kmouss
manager: jeconnoc
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
ms.openlocfilehash: 70aec41c885ab81371f5318f7557b0e628ac3308
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915411"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Co dělat v případě, že přerušení služby Azure má dopad na virtuálních počítačích Azure
Ve společnosti Microsoft můžeme fungovat pevného a ujistěte se, že našich služeb jsou vždy k dispozici a když je potřebujete. Vynutí nad rámec naše řízení někdy vliv nám způsoby, které způsobit přerušení poskytování služeb neplánované.

Společnost Microsoft poskytuje smlouvy úroveň služeb (SLA) pro jeho služby jako závazek provozu a připojení. Smlouva SLA pro jednotlivé služby Azure najdete na [smlouvy o úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/).

Azure již obsahuje mnoho funkcí integrovanou platformu, které podporují aplikace s vysokou dostupností. Další informace o těchto službách, přečtěte si [zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Tento článek se zabývá true zotavení po havárii, když dojde výpadku způsobeného hlavní přírodní katastrofě nebo přerušení služeb rozšířeným celou oblast. Toto jsou výjimečných výskytů, ale je nutné připravit možnost, že je k výpadku celou oblast. Když celou oblast dojde přerušení služby, místně redundantní kopie dat by být dočasně k dispozici. Pokud jste povolili geografická replikace, tři další kopie objektů BLOB služby Azure Storage a tabulek jsou uložené v jiné oblasti. V případě výpadku dokončení místní nebo havárii, ve kterém není použitelná pro obnovení primární oblasti znovu mapuje Azure, všechny položky DNS pro danou oblast geograficky replikované.

Chcete-li zpracovat tyto výjimečných výskytů, poskytujeme následující pokyny pro virtuální počítače Azure v případě přerušení služby celé oblasti, kde je virtuální počítač Azure aplikaci nasadit.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Možnost 1: Spuštění převzetí služeb při selhání pomocí Azure Site Recovery
Azure Site Recovery pro virtuální počítače můžete nakonfigurovat tak, aby vaše aplikace s jedním kliknutím v několika minut můžete obnovit. Můžete replikovat do oblasti Azure podle svého výběru a není omezen na spárované oblasti. Abyste mohli začít podle [replikaci virtuálních počítačů](https://aka.ms/a2a-getting-started). Můžete [vytvoření plánu obnovení](../site-recovery/site-recovery-create-recovery-plans.md) tak, aby můžete automatizovat proces celý převzetí služeb při selhání pro vaši aplikaci. Můžete [testování vaší převzetí služeb při selhání](../site-recovery/site-recovery-test-failover-to-azure.md) předem, bez dopadu na produkční aplikace nebo probíhající replikace. V případě přerušení primární oblasti, jste právě [zahájení převzetí služeb při selhání](../site-recovery/site-recovery-failover.md) a převeďte aplikace v cílové oblasti.


## <a name="option-2-wait-for-recovery"></a>Možnost 2: Čekání pro obnovení
V takovém případě není třeba žádné akce z vaší strany. Vědět, že pracujeme usilovně obnovit dostupnost služeb. Zobrazí aktuální stav služby na našem [řídicím panelu stavu služeb Azure](https://azure.microsoft.com/status/).

Toto je nejlepší možnost, pokud jste nenastavili Azure Site Recovery, geograficky redundantní úložiště s přístupem pro čtení nebo geograficky redundantní úložiště před přerušení. Pokud jste se nastavili geograficky redundantní úložiště nebo geograficky redundantní úložiště s přístupem pro čtení pro účet úložiště, kde jsou uloženy vaše virtuální počítač virtuální pevné disky (VHD), můžete zobrazovat obnovit základní image virtuálního pevného disku a opakujte ke zřízení nového virtuálního počítače z něj. Toto není upřednostňovanou možnost, protože nejsou k dispozici žádné záruky synchronizace dat. V důsledku toho tato možnost není zaručena.


> [!NOTE]
> Uvědomte si, že nemáte žádné kontrolu nad tento proces a se vztahuje pouze na přerušení služeb celou oblast. Z tohoto důvodu musíte také spoléhat na další specifické pro aplikaci Zálohování strategie k dosažení nejvyšší úroveň dostupnosti. Další informace najdete v části na [Data strategie zotavení po havárii](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery).
>
>

## <a name="next-steps"></a>Další postup

- Spustit [chrání vaše aplikace spuštěné na virtuálních počítačích Azure](https://aka.ms/a2a-getting-started) pomocí Azure Site Recovery

- Další informace o tom, jak implementovat strategie vysoké dostupnosti a zotavení po havárii, najdete v části [zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- K vývoji podrobné technické vysvětlení funkcí Cloudová platforma, najdete v části [technické pokyny Azure odolnosti](../resiliency/resiliency-technical-guidance.md).


- Pokud tyto pokyny jsou vymazat, nebo pokud chcete, aby operace vaším jménem, obraťte se na [zákaznickou podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
