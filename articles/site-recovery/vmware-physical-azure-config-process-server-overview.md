---
title: O konfiguraci Azure Site Recovery, proces a hlavní cílové servery | Dokumentace Microsoftu
description: Tento článek poskytuje přehled o konfiguraci, proces a hlavní cílové servery pomocí při nastavování zotavení po havárii místních virtuálních počítačů VMware do Azure pomocí Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 8b3815fc9dc44484779a70b51ebff4802265d53a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417738"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>O součástech Site Recovery (konfigurační, proces, hlavní cílový)

Tento článek popisuje konfiguraci, proces a hlavní cílové servery používané při replikaci virtuálních počítačů VMware a fyzických serverů do Azure s využitím [Site Recovery](site-recovery-overview.md) služby.

## <a name="configuration-server"></a>Konfigurační server

Pro zotavení po havárii místních virtuálních počítačů VMware a fyzických serverů, je třeba Site Recovery v místním nasazení konfiguračního serveru.

**Nastavení** | **Podrobnosti** | **Odkazy**
--- | --- | ---
**Součásti**  | Konfigurace počítače serveru spustí všechny místní komponenty Site Recovery, mezi které patří konfigurační server, procesový server a hlavní cílový server.<br/><br/> Když nastavíte konfigurační server, jsou všechny součásti nainstalovány automaticky. | [Čtení](vmware-azure-common-questions.md#configuration-server) konfigurační server – nejčastější dotazy.
**Role** | Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat. | Další informace o architektuře pro [VMware](vmware-azure-architecture.md) a [fyzický server](physical-azure-architecture.md) zotavení po havárii do Azure.
**Požadavků na VMware** | Pro zotavení po havárii místních virtuálních počítačů VMware musíte nainstalovat a spustit konfigurační server jako místní, vysoce dostupný virtuální počítač VMware. | [Další informace o](vmware-azure-deploy-configuration-server.md#prerequisites) požadavky.
**Nasazení VMware** | Doporučujeme, abyste nasazení konfiguračního serveru pomocí ve stažené šabloně OVA. Tato metoda poskytuje jednoduše způsob, jak nastavit konfigurační server, která splňuje všechny požadavky a požadavky.<br/><br/> Pokud z nějakého důvodu nemůžete k nasazení virtuálního počítače VMware pomocí šablony vajíčka, můžete nastavit konfiguraci serverových počítačů ručně, jak je popsáno níže pro zotavení po havárii fyzického počítače. | [Nasazení](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) pomocí OVA šablony.
**Požadavky na fyzický server** | Pro zotavení po havárii na místních fyzických serverech můžete nasazení konfiguračního serveru ručně. | [Další informace o](physical-azure-set-up-source.md#prerequisites) požadavky.
**Nasazení fyzického serveru** | Pokud není možné nainstalovat jako virtuální počítač VMware, můžete jej nainstalovat na fyzickém serveru. | [Nasazení](physical-azure-set-up-source.md#set-up-the-source-environment) konfiguračního serveru ručně.


## <a name="process-server"></a>Procesový server

**Nastavení** | **Podrobnosti** | **Odkazy**
--- | --- | ---
**Nasazení**  | Pro replikaci místních virtuálních počítačů VMware a fyzických serverů a zotavení po havárii, budete potřebovat procesový server v místním prostředí. Ve výchozím nastavení je na procesovém serveru nainstalované na konfiguračním serveru při nasazování. | [Další informace](vmware-azure-architecture.md?#architectural-components).
**Role (v místním** | -Přijímá data replikace z počítače povolena replikace.<br/> -Optimalizuje data replikace pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do služby Azure Storage.<br/> -Provádí nabízenou instalaci služby Mobility Site Recovery na místních virtuálních počítačů VMware a fyzické servery, které chcete replikovat.<br/> -Provádí automatické zjišťování místních počítačů. | [Další informace](vmware-physical-azure-config-process-server-overview.md#process-server). 
**Role (navrácení služeb po obnovení z Azure)** | Po převzetí služeb při selhání z místní lokality je nastavit procesový server v Azure, jako virtuální počítač Azure, ke zpracování navrácení služeb po obnovení na vaše místní umístění.<br/><br/> Procesový server v Azure je dočasný. Po dokončení navrácení služeb po obnovení je možné odstranit virtuální počítač Azure. | [Další informace](vmware-azure-set-up-process-server-azure.md).
**Škálování** | U větších nasazení pro místní můžete nastavit další, horizontální navýšení kapacity procesových serverů. Další servery horizontálně navýšit kapacitu, zpracovává velký počet replikaci počítačů a větší objemy přenosů replikace.<br/><br/> Počítače můžou přesouvat mezi dvěma procesových serverů, aby bylo možné vyrovnávat zatížení replikace provozu. | [Další informace](vmware-azure-set-up-process-server-scale.md),


## <a name="master-target-server"></a>Hlavní cílový server

Hlavní cílový server zpracovává replikační data během navrácení služeb z Azure po obnovení.

- Ve výchozím nastavení na konfiguračním serveru je nainstalovaná.
- Pro velká nasazení můžete přidat další, samostatný hlavní cílový server navrácení služeb po obnovení.


## <a name="next-steps"></a>Další postup
- Zkontrolujte [architektura](vmware-azure-architecture.md) pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů.
- Zkontrolujte [požadavky a předpoklady](vmware-physical-azure-support-matrix.md) pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure. 
