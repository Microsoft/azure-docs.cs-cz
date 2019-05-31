---
title: Monitorování Azure Site Recovery procesový server
description: Tento článek popisuje, jak monitorovat Azure Site Recovery procesový server.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 4ff52e737438210296b8f2201d5e66e1d38b7bc9
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418281"
---
# <a name="monitor-the-process-server"></a>Monitorování procesový server

Tento článek popisuje, jak monitorovat [Site Recovery](site-recovery-overview.md) procesový server.

- Procesový server se používá při nastavování zotavení po havárii místních virtuálních počítačů VMware a fyzických serverů do Azure.
- Ve výchozím nastavení procesový server běží na konfiguračním serveru. Je nainstalovaná ve výchozím nastavení při nasazení konfiguračního serveru.
- V případě potřeby škálování a zpracovávají velké objemy replikované počítače a zvýšit objem provozu replikace, můžete nasadit další, horizontální navýšení kapacity procesových serverů.

[Další informace](vmware-physical-azure-config-process-server-overview.md) o roli a nasazení procesových serverů.

## <a name="monitoring-overview"></a>Přehled monitorování

Vzhledem k tomu, že procesový server má mnoho role, zejména ukládání replikovaných dat do mezipaměti, kompresi a přenos do Azure, je důležité monitorovat stav zpracování procesu serveru průběžně.

Existuje mnoho situací, které často ovlivňují výkon procesu serveru. Problémy neovlivňují výkon bude mít požadovaného kaskádového efektu na stav virtuálního počítače, případně doručením (push) na procesovém serveru a jeho replikované počítače v kritickém stavu. Situace:

- Vysoký počet virtuálních počítačů pomocí procesového serveru, blíží nebo překračuje doporučené omezení.
- Tento procesový server používají virtuální počítače mají s vysokou četností změn dat sazby.
- Propustnost sítě mezi virtuálními počítači a že procesový server není dost informací k nahrání dat replikace k procesového serveru.
- Propustnost sítě mezi procesovým serverem a Azure není dostatečná k odesílání dat replikace z procesového serveru do Azure.

Všechny tyto problémy mohou ovlivnit plánovaného bodu obnovení (RPO) virtuálních počítačů. 

**Proč?** Protože všechny disky na virtuálním počítači mají společný bod generování bod obnovení pro virtuální počítač vyžaduje. Pokud má jeden disk s vysokou četností změn dat sazby, je replikace pomalá nebo procesový server nejsou optimálním řešením, ovlivňuje to, jak efektivní jsou vytvořeny body obnovení.

## <a name="monitor-proactively"></a>Proaktivní monitorování

Abyste zabránili problémům s procesový server, je potřeba:

- Vysvětlení specifické požadavky na procesových serverů pomocí [kapacity a pokyny k dimenzování](site-recovery-plan-capacity-vmware.md#capacity-considerations)a ujistěte se, že proces servery jsou nasazené a spouštění podle doporučení.
- Monitorování výstrah a řešení potíží, když k nim dojde, zajištění efektivního provozu procesových serverů.


## <a name="process-server-alerts"></a>Upozornění k procesového serveru

Procesový server generuje počet upozornění na stav, uvedené v následující tabulce.

**Typ výstrahy** | **Podrobnosti**
--- | ---
![V pořádku][green] | Procesový server je v pořádku a připojené.
![Upozornění][yellow] | Využití procesoru > 80 % po dobu posledních 15 minut
![Upozornění][yellow] | > 80 % využití paměti za posledních 15 minut
![Upozornění][yellow] | Mezipaměť složky volného místa < 30 % po dobu posledních 15 minut
![Upozornění][yellow] | Služby procesového serveru nejsou spuštěné za posledních 15 minut
![Kritická][red] | Využití procesoru > 95 % po dobu posledních 15 minut
![Kritická][red] | > 95 % využití paměti za posledních 15 minut
![Kritická][red] | Složka mezipaměti volné místo < 25 % za posledních 15 minut
![Kritická][red] | Žádný prezenční signál z procesového serveru po dobu 15 minut.

![klíč tabulky](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Nejhorší výstrahy vygenerované podle celkového stavu procesového serveru.



## <a name="monitor-process-server-health"></a>Monitorování procesu serveru stavu

Můžete monitorovat stav procesových serverů následujícím způsobem: 

1. K monitorování stavu replikace a stav replikovaného počítače a jeho procesový server v trezoru > **replikované položky**, klikněte na počítač, který chcete sledovat.
2. V **stav replikace**, můžete sledovat stav virtuálního počítače. Klikněte na stavu k podrobnostem pro podrobnosti o chybě.

    ![Stav zpracování procesu serveru na řídicím panelu virtuálních počítačů](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. V **stav zpracování procesu serveru**, můžete monitorovat stav procesového serveru. Přejít na nižší úroveň podrobnosti.

    ![Podrobnosti procesového serveru na řídicím panelu virtuálních počítačů](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Stav je možné také monitorovat pomocí grafické vyjádření na stránce virtuálního počítače.
    - Horizontální navýšení kapacity procesového serveru budou zvýrazněna oranžově, pokud existují upozornění s ním spojená a červenou, když má všechny kritické problémy. 
    - Pokud na procesovém serveru běží ve výchozím nasazení konfiguračního serveru, bude konfigurační server zvýrazněny odpovídajícím způsobem.
    - Chcete přejít k podrobnostem, klikněte na tlačítko na konfiguračním serveru nebo procesového serveru. Poznámka: všechny problémy a jakýchkoli doporučení pro nápravu.

Můžete také sledovat zpracovat servery v trezoru v části **infrastruktura Site Recovery**. V **spravovat vaše infrastruktura Site Recovery**, klikněte na tlačítko **konfigurační servery**. Vyberte konfigurační server přidružené k procesového serveru a přejít dolů do podrobnosti procesového serveru.


## <a name="next-steps"></a>Další postup

- Pokud máte některá proces servery problémy, postupujte podle našich [pokyny při řešení potíží](vmware-physical-azure-troubleshoot-process-server.md)
- Pokud potřebujete další pomoc, zveřejněte svůj dotaz v [fórum pro Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
