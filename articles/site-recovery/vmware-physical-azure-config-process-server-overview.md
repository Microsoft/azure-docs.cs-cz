---
title: O konfiguraci azure site recovery nebo procesu/hlavní cílové servery
description: Tento článek poskytuje přehled konfigurace, procesu a hlavní cílové servery pomocí při nastavování zotavení po havárii místních virtuálních počítačů VMware do Azure s Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062091"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Součásti obnovení lokality (konfigurace, proces, hlavní cíl)

Tento článek popisuje konfigurace, proces a hlavní cílové servery používané službou [Site Recovery](site-recovery-overview.md) k replikaci virtuálních počítačů VMware a fyzických serverů do Azure.

## <a name="configuration-server"></a>Konfigurační server

Pro zotavení po havárii místních virtuálních počítačů VMware a fyzických serverů nasaďte místní konfigurační server Site Recovery.

**Nastavení** | **Podrobnosti** | **Odkazy**
--- | --- | ---
**Komponenty**  | Počítač konfiguračního serveru spouští všechny místní součásti site recovery, mezi něž patří konfigurační server, procesní server a hlavní cílový server.<br/><br/> Při nastavovacím serveru jsou všechny součásti nainstalovány automaticky. | [Přečtěte si](vmware-azure-common-questions.md#configuration-server) nejčastější dotazy konfiguračního serveru.
**Role** | Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat. | Přečtěte si další informace o architektuře pro [vmware](vmware-azure-architecture.md) a [zotavení po havárii fyzického serveru](physical-azure-architecture.md) do Azure.
**Požadavky na vmware** | Pro zotavení po havárii místních virtuálních počítačů VMware je nutné nainstalovat a spustit konfigurační server jako místní, vysoce dostupný virtuální počítač VMware. | [Seznamte](vmware-azure-deploy-configuration-server.md#prerequisites) se s požadavky.
**Nasazení vMware** | Doporučujeme nasadit konfigurační server pomocí stažené šablony OVA. Tato metoda poskytuje jednoduchý způsob, jak nastavit konfigurační server, který splňuje všechny požadavky a požadavky.<br/><br/> Pokud z nějakého důvodu nemůžete nasadit virtuální počítač VMware pomocí šablony OVA, můžete nastavit počítače konfiguračního serveru ručně, jak je popsáno níže pro zotavení po havárii fyzického počítače. | [Nasazení](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) pomocí šablony OVA.
**Požadavky na fyzický server** | Pro zotavení po havárii na místních fyzických serverech nasadíte konfigurační server ručně. | [Seznamte](physical-azure-set-up-source.md#prerequisites) se s požadavky.
**Fyzické nasazení serveru** | Pokud jej nelze nainstalovat jako virtuální počítač VMware, můžete jej nainstalovat na fyzický server. | [Nasadit](physical-azure-set-up-source.md#set-up-the-source-environment) konfigurační server ručně.

## <a name="process-server"></a>Procesní server

Procesní server zpracovává data replikace během převzetí služeb při selhání a navrácení služeb po selhání a nainstaluje službu Mobility pro místní virtuální počítačvsystému VMware a fyzické servery.

**Nastavení** | **Podrobnosti** | **Odkazy**
--- | --- | ---
**Nasazení**  | Ve výchozím nastavení je při nasazení konfiguračního serveru nainstalován procesní server. <br/><br/> Místní procesní server je potřebný pro zotavení po havárii a replikaci místních virtuálních počítačů VMware a fyzických serverů. | [Další informace](vmware-azure-architecture.md#architectural-components).
**Role (místní)** | Přijímá data replikace z počítačů povolených pro replikaci. <br/><br/> Optimalizuje replikační data pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do Služby Azure Storage. <br/><br/> Provádí nabízenou instalaci služby Site Recovery Mobility na místních virtuálních počítačích VMware a fyzických serverech, které chcete replikovat. <br/><br/> Provádí automatické zjišťování místních počítačů. | [Další informace](vmware-azure-enable-replication.md).
**Role (navrácení služeb po selhání z Azure)** | Po převzetí služeb při selhání z vašeho místního webu nastavíte procesní server v Azure jako virtuální počítač Azure, který bude zpracovávat navrácení služeb po selhání do místního umístění.<br/><br/> Procesní server v Azure je dočasný. Virtuální počítač Azure se dá odstranit po dokončení navrácení služeb po selhání. | [Další informace](vmware-azure-set-up-process-server-azure.md).
**Měřítka** | Pro větší nasazení, místní můžete nastavit další, horizontální navýšení kapacity procesu servery. Další servery vertikálně kapacitu, zpracováním větší počet replikačních strojů a větší objemy replikace provozu.<br/><br/> Počítače můžete přesouvat mezi dvěma procesními servery, aby bylo možné načíst zatížení replikačních přenosů. | [Další informace](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Hlavní cílový server

Hlavní cílový server zpracovává replikační data během navrácení služeb z Azure po obnovení.

- Ve výchozím nastavení je hlavní cílový server nainstalován na konfiguračním serveru.
- U velkých nasazení můžete přidat další samostatný hlavní cílový server pro navrácení služeb po selhání.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte [architekturu](vmware-azure-architecture.md) pro zotavení po havárii virtuálních virtuálních měn VMware a fyzických serverů.
- Zkontrolujte [požadavky a předpoklady](vmware-physical-azure-support-matrix.md) pro zotavení po havárii virtuálních počítačích VMware a fyzických serverů do Azure.
