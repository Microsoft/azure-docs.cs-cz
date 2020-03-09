---
title: Informace o Azure Site Recovery konfigurace/proces/hlavní cílové servery
description: Tento článek poskytuje přehled konfigurace, procesu a hlavních cílových serverů pomocí při nastavování zotavení po havárii místních virtuálních počítačů VMware do Azure pomocí Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: ad816f39dd4182dfa41fca975c99824a5d77f860
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394998"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Informace o komponentách Site Recovery (konfigurace, proces, hlavní cíl)

Tento článek popisuje konfigurační, procesní a hlavní cílové servery, které se používají při replikaci virtuálních počítačů VMware a fyzických serverů do Azure pomocí služby [Site Recovery](site-recovery-overview.md) .

## <a name="configuration-server"></a>Konfigurační server

Pro zotavení po havárii místních virtuálních počítačů VMware a fyzických serverů budete potřebovat Site Recovery konfigurační server nasazený místně.

**Nastavení** | **Podrobnosti** | **Odkazy**
--- | --- | ---
**Konstrukční**  | Počítač konfiguračního serveru spouští všechny místní Site Recovery komponenty, mezi které patří konfigurační server, procesový Server a hlavní cílový server.<br/><br/> Při nastavování konfiguračního serveru jsou všechny součásti nainstalovány automaticky. | [Přečtěte](vmware-azure-common-questions.md#configuration-server) si nejčastější dotazy ke konfiguračnímu serveru.
**Role** | Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat. | Přečtěte si další informace o architektuře pro zotavení po havárii [VMware](vmware-azure-architecture.md) a [fyzických serverů](physical-azure-architecture.md) do Azure.
**Požadavky VMware** | V případě zotavení po havárii místních virtuálních počítačů VMware je nutné nainstalovat a spustit konfigurační server jako místní a vysoce dostupný virtuální počítač VMware. | [Přečtěte si informace o](vmware-azure-deploy-configuration-server.md#prerequisites) požadavcích.
**Nasazení VMware** | Doporučujeme nasadit konfigurační server pomocí stažené šablony vajíček. Tato metoda poskytuje jednoduše způsob, jak nastavit konfigurační server, který bude splňovat všechny požadavky a požadavky.<br/><br/> Pokud z nějakého důvodu nemůžete nasadit virtuální počítač VMware pomocí šablony vajíček, můžete nastavit počítače konfiguračního serveru ručně, jak je popsáno níže pro zotavení po havárii fyzického počítače. | [Nasaďte](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) se šablonou vajíček.
**Požadavky na fyzický server** | Pro zotavení po havárii na místních fyzických serverech nasadíte konfigurační server ručně. | [Přečtěte si informace o](physical-azure-set-up-source.md#prerequisites) požadavcích.
**Nasazení fyzického serveru** | Pokud se nedá nainstalovat jako virtuální počítač VMware, můžete ho nainstalovat na fyzický server. | [Nasaďte](physical-azure-set-up-source.md#set-up-the-source-environment) konfigurační server ručně.


## <a name="process-server"></a>Procesový Server

**Nastavení** | **Podrobnosti** | **Odkazy**
--- | --- | ---
**Nasazení**  | Pro zotavení po havárii a replikaci místních virtuálních počítačů VMware a fyzických serverů budete potřebovat procesový Server v místním prostředí. Ve výchozím nastavení se procesový Server nainstaluje na konfigurační server, když ho nasadíte. | [Další informace](vmware-azure-architecture.md?#architectural-components).
**Role (místně** | – Přijímá replikační data z počítačů, které jsou povoleny pro replikaci.<br/> – Optimalizuje replikační data pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do Azure Storage.<br/> – Provede nabízenou instalaci služby Site Recovery mobility na místních virtuálních počítačích VMware a fyzických serverech, které chcete replikovat.<br/> – Provádí automatické zjišťování místních počítačů. | [Další informace](vmware-physical-azure-config-process-server-overview.md#process-server). 
**Role (navrácení služeb po obnovení z Azure)** | Po převzetí služeb při selhání z vaší místní lokality nastavíte procesový Server v Azure jako virtuální počítač Azure pro zpracování navrácení služeb po obnovení do místního umístění.<br/><br/> Procesový Server v Azure je dočasný. Po navrácení služeb po obnovení se virtuální počítač Azure dá odstranit. | [Další informace](vmware-azure-set-up-process-server-azure.md).
**Škálování** | U větších nasazení v místním prostředí můžete nastavit další procesové servery se škálováním na více instancí. Další servery škálují kapacitu, zpracovává větší počet replikačních počítačů a větší objemy replikačních přenosů.<br/><br/> Aby bylo možné vyrovnávat zatížení provozu replikace, můžete přesunout počítače mezi dvěma procesových serverů. | [Další informace](vmware-azure-set-up-process-server-scale.md)


## <a name="master-target-server"></a>Hlavní cílový server

Hlavní cílový server zpracovává replikační data během navrácení služeb z Azure po obnovení.

- Instaluje se ve výchozím nastavení na konfiguračním serveru.
- U rozsáhlých nasazení můžete přidat další samostatný hlavní cílový server pro navrácení služeb po obnovení.


## <a name="next-steps"></a>Další kroky
- Projděte si [architekturu](vmware-azure-architecture.md) pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů.
- Přečtěte si [požadavky a předpoklady](vmware-physical-azure-support-matrix.md) pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure. 
