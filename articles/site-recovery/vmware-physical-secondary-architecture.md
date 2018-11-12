---
title: Architektura pro zotavení po havárii serveru VMware/fyzických prostředků do sekundární lokality pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje přehled komponent a architektury používané při zotavení po havárii místních virtuálních počítačů VMware nebo fyzické servery Windows/Linux do sekundární lokality VMware pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: raynew
ms.openlocfilehash: 77387b52003923dffce7519df9cca47fad8f6e6c
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007429"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>Architektury pro replikaci serveru VMware/fyzických prostředků do sekundární místní lokality

Tento článek popisuje, architektury a procesy používané při nastavení replikace pro zotavení po havárii, převzetí služeb při selhání a obnovení virtuálních počítačů (VM) v místním VMware nebo fyzické servery Windows/Linux do sekundární lokality VMware pomocí [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Komponenty architektury

**Oblast** | **Komponenta** | **Podrobnosti**
--- | --- | ---
**Azure** | Tento scénář nasadíte pomocí nástroje InMage Scout. | K získání nástroje InMage Scout potřebujete předplatné Azure.<br/><br/> Po vytvoření trezoru služby Recovery Services si stáhnete InMage Scout a nainstalujete nejnovější aktualizace pro nastavení nasazení.
**Procesový server** | Umístěný v primární lokalitě | Procesový server nasadíte, aby se staral o ukládání do mezipaměti, kompresi a optimalizaci dat.<br/><br/> Také obstará nabízenou instalaci nástroje Unified Agent na počítače, které chcete chránit.
**Konfigurační server** | Umístěný v sekundární lokalitě | Konfigurační server spravuje, konfiguruje a monitoruje nasazení – buď pomocí webu pro správu, nebo konzoly vContinuum.
**Server vContinuum** | Volitelné. Instaluje se do stejné lokality jako konfigurační server. | Poskytuje konzoli pro správu a monitorování chráněného prostředí.
**Hlavní cílový server** | Umístěný v sekundární lokalitě | Na hlavní cílový server se ukládají replikovaná data. Přijímá data z procesního serveru, vytváří repliku počítače v sekundární lokalitě a ukládá body pro uchovávání dat.<br/><br/> Počet hlavních cílových serverů, které potřebujete, závisí na počtu počítačů, které chráníte.<br/><br/> Pokud chcete po obnovení vrátit služby do primární lokality, potřebujete hlavní cílový server i tam. Na tento server je třeba nainstalovat Unified Agent.
**VMware ESX/ESXi a server vCenter** |  Virtuální počítače jsou hostované na hostitelích ESX nebo ESXi. Hostitelé se spravují pomocí serveru vCenter. | Pro replikaci virtuálních počítačů VMware budete potřebovat infrastrukturu VMware.
**Virtuální počítače / fyzické servery** |  Nástroj Unified Agent nainstalovaný na virtuálních počítačích nebo fyzických serverech VMware, které chcete replikovat. | Agent funguje jako zprostředkovatel komunikace mezi všemi součástmi.

### <a name="replication-process"></a>Proces replikace

1. V každé lokalitě si nastavte servery představující jednotlivé součásti (konfigurační, procesní, hlavní cílový) a nainstalujte si Unified Agent na počítače, které chcete replikovat.
2. Po počáteční replikaci odesílá agent na každém počítači na procesní server rozdílové replikační změny.
3. Procesní server tato data optimalizuje a přenese je na hlavní cílový server v sekundární lokalitě. Konfigurační server spravuje replikační proces.

**Obr. 6: Replikace z VMware do VMware**

![Z VMware do VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Další postup

[Nastavit](vmware-physical-secondary-disaster-recovery.md) zotavení po havárii virtuálních počítačů VMware a fyzických serverů do sekundární lokality.
