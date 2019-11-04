---
title: Nadcházející vyřazení DR mezi weby vlastněné zákazníkem pomocí technologie Hyper-V a mezi lokalitami spravovanými SCVMM do Azure | Microsoft Docs
description: Podrobnosti o nadcházejících vyřazení DR mezi weby vlastněné zákazníkem pomocí technologie Hyper-V a mezi lokalitami spravovanými SCVMM do Azure a alternativními možnostmi
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/13/2019
ms.author: rajanaki
ms.openlocfilehash: 68881d57f251d2f26996b2a837780106326ec1d3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492289"
---
# <a name="upcoming-deprecation-of-dr-between-customer-owned-sites-using-hyper-v-and-between-sites-managed-by-scvmm-to-azure"></a>Nadcházející vyřazení DR mezi weby vlastněné zákazníkem pomocí technologie Hyper-V a mezi lokalitami spravovanými SCVMM do Azure

Tento článek popisuje nadcházející plán vyřazení, odpovídající důsledky a alternativní možnosti, které jsou zákazníkům k dispozici pro následující scénáře, které byly podporovány nástrojem Azure Site Recovery. 

- Zotavení po havárii mezi virtuálními počítači Hyper-V, které spravuje SCVMM mezi weby vlastněné zákazníkem 
- DR virtuálních počítačů Hyper-V spravovaných SCVMM do Azure

> [!IMPORTANT]
> Tyto scénáře jsou aktuálně označeny pro vyřazení a u zákazníků se očekává, že se nejdříve převezmou kroky k nápravě, aby se zabránilo jakémukoli výpadku jejich prostředí. 
 

## <a name="what-changes-should-you-expect"></a>Jaké změny byste měli očekávat?

- Od listopadu 2019 nebudou pro tyto scénáře povoleny žádné nové uživatele. **Stávající replikace a operace správy** , včetně převzetí služeb při selhání, testovacího převzetí služeb při selhání, monitorování atd. **nebudou ovlivněny**.

- Když jsou scénáře zastaralé, dojde k následujícím důsledkům, pokud zákazník nebude postupovat podle doporučení.

    - DR mezi virtuálními počítači Hyper-V spravovaných pomocí SCVMM mezi weby vlastněné zákazníkem: replikace bude i nadále fungovat, protože základní funkce repliky technologie Hyper-V bude fungovat i nadále, ale zákazníci nebudou moci zobrazovat, spravovat ani provádět žádné operace související s nástrojem DR. prostřednictvím prostředí Azure Sire Recovery v Azure Portal. 
    - DR virtuálních počítačů Hyper-V spravovaných pomocí SCVMM do Azure: dojde k přerušení stávajících replikací a zákazníci nebudou moct zobrazit, spravovat ani provádět žádné operace s nástrojem DR pomocí Azure Site Recovery


## <a name="recommended-actions-to-be-taken"></a>Doporučené akce, které mají být provedeny

Níže jsou uvedené možnosti, které zákazník potřebuje k zajištění, aby strategie zotavení po havárii neovlivnila, když je scénář zastaralý. 

- Vyberte, [že chcete začít používat Azure jako cíl Dr pro virtuální počítače na hostitelích Hyper-V](hyper-v-azure-tutorial.md).

> [!IMPORTANT]
> Upozorňujeme, že vaše místní prostředí může mít pořád SCVMMM, ale konfigurujete ASR pomocí odkazů jenom na hostitele Hyper-V.

- Vyberte, pokud chcete pokračovat v replikaci mezi lokalitami, ale s využitím základního [řešení repliky technologie Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica), ale nebude možné spravovat konfigurace DR pomocí Azure Site Recovery v Azure Portal. 


## <a name="next-steps"></a>Další kroky
Naplánujte si vyřazení a vyberte jinou možnost, která je nejvhodnější pro vaši infrastrukturu a podnikání. V případě, že máte dotazy týkající se tohoto přístupu, obraťte se na podpora Microsoftu

