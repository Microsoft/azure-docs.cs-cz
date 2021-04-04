---
title: Znovu nastavte ochranu virtuálních počítačů Azure do primární oblasti pomocí Azure Site Recovery | Microsoft Docs
description: Popisuje, jak znovu chránit virtuální počítače Azure po převzetí služeb při selhání, sekundární do primární oblasti, pomocí Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 3b9edab6e908b4506a92c78aa8f3f53277b9c17b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91360867"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Znovunastavení ochrany virtuálních počítačů Azure, u kterých proběhlo převzetí služeb při selhání, v primární oblasti

Při [převzetí služeb při selhání](site-recovery-failover.md) virtuálních počítačů Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md)se virtuální počítače spustí v sekundární oblasti v **nechráněném** stavu. Pokud chcete převzít služby při selhání back-VM do primární oblasti, proveďte následující úlohy:

1. Znovu nastavte ochranu virtuálních počítačů v sekundární oblasti, aby se začaly replikovat do primární oblasti.
1. Po dokončení ochrany a replikaci virtuálních počítačů můžete převzít služby při selhání ze sekundární do primární oblasti.

## <a name="prerequisites"></a>Požadavky

- Převzetí služeb virtuálního počítače při selhání z primární do sekundární oblasti musí být potvrzené.
- Primární cílový webový server by měl být dostupný a měl by být přístupný nebo vytvářet prostředky v této oblasti.

## <a name="reprotect-a-vm"></a>Opětovné zapnutí ochrany virtuálního počítače

1. V **trezoru**  >  **replikované položky** klikněte pravým tlačítkem na virtuální počítač převzetí služeb při selhání a vyberte **znovu zapnout ochranu**. Směr další ochrany by se měl zobrazit ze sekundárního na primární.

   ![Snímek obrazovky zobrazuje virtuální počítač s místní nabídkou, která má vybranou možnost znovu nastavit ochranu.](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Zkontrolujte skupinu prostředků, síť, úložiště a skupiny dostupnosti. Pak klikněte na **OK**. Pokud jsou nějaké prostředky označené jako nové, vytvoří se jako součást procesu nové ochrany.
1. V rámci úlohy ochrany se v cílové lokalitě dokončí nejnovější data. Po dokončení úlohy bude provedena rozdílová replikace. Pak můžete převzít služby při selhání zpět do primární lokality. Pomocí možnosti přizpůsobit můžete vybrat účet úložiště nebo síť, kterou chcete použít při opětovném zapnutí ochrany.

   ![Přizpůsobení možnosti](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Přizpůsobení nastavení opětovné ochrany

Během ochrany můžete přizpůsobit následující vlastnosti cílového virtuálního počítače.

![Přizpůsobení](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Vlastnost |Poznámky  |
|---------|---------|
|Cílová skupina prostředků | Upravte cílovou skupinu prostředků, ve které se virtuální počítač vytvoří. V rámci ochrany je cílový virtuální počítač odstraněný. Můžete zvolit novou skupinu prostředků, ve které se po převzetí služeb při selhání vytvoří virtuální počítač. |
|Cílová virtuální síť | Cílovou síť nelze změnit během úlohy opětovného zapnutí ochrany. Chcete-li změnit síť, proveďte znovu mapování sítě. |
|Cílové úložiště (sekundární virtuální počítač nepoužívá spravované disky) | Můžete změnit účet úložiště, který virtuální počítač používá po převzetí služeb při selhání. |
|Spravované disky repliky (sekundární virtuální počítač používá spravované disky) | Site Recovery vytvoří repliku spravované disky v primární oblasti pro zrcadlení spravovaných disků sekundárního virtuálního počítače. |
|Úložiště mezipaměti | Můžete zadat účet úložiště mezipaměti, který se použije při replikaci. Ve výchozím nastavení se vytvoří nový účet úložiště mezipaměti, pokud neexistuje. |
|Skupina dostupnosti | Pokud je virtuální počítač v sekundární oblasti součástí skupiny dostupnosti, můžete zvolit skupinu dostupnosti pro cílový virtuální počítač v primární oblasti. Ve výchozím nastavení se Site Recovery pokusí najít existující skupinu dostupnosti v primární oblasti a použít ji. Během přizpůsobení můžete zadat novou skupinu dostupnosti. |

### <a name="what-happens-during-reprotection"></a>Co se stane během ochrany?

Ve výchozím nastavení dojde k následujícímu:

1. Účet úložiště mezipaměti se vytvoří v oblasti, ve které běží virtuální počítač pro převzetí služeb při selhání.
1. Pokud cílový účet úložiště (původní účet úložiště v primární oblasti) neexistuje, vytvoří se nový. Přiřazený název účtu úložiště je název účtu úložiště používaného sekundárním virtuálním počítačem, který je s příponou `asr` .
1. Pokud virtuální počítač používá spravované disky, v primární oblasti se vytvoří replika spravované disky pro ukládání dat replikovaných z disků sekundárního virtuálního počítače.
1. Pokud cílová skupina dostupnosti neexistuje, vytvoří se nová v rámci úlohy opětovného ochrany v případě potřeby. Pokud jste upravili nastavení ochrany, použije se vybraná sada.

Když aktivujete úlohu opětovné ochrany a cílový virtuální počítač existuje, dojde k následujícímu:

1. Cílový virtuální počítač je vypnutý, pokud je spuštěný.
1. Pokud virtuální počítač používá spravované disky, vytvoří se kopie původního disku s `-ASRReplica` příponou. Původní disky se odstraní. `-ASRReplica`Kopie se používají pro replikaci.
1. Pokud virtuální počítač používá nespravované disky, jsou datové disky cílového virtuálního počítače odpojeny a použity pro replikaci. Na virtuálním počítači se vytvoří a připojí kopie disku s operačním systémem. Původní disk s operačním systémem je odpojený a používá se k replikaci.
1. Synchronizují se jenom změny mezi zdrojovým diskem a cílovým diskem. Rozdíly jsou vypočítány porovnáním disků a pak přenesených. Níže najdete informace o odhadované době pro dokončení ochrany.
1. Po dokončení synchronizace se spustí rozdílová replikace a v souladu se zásadami replikace se vytvoří bod obnovení.

Když aktivujete úlohu opětovné ochrany a cílový virtuální počítač a disky neexistují, dojde k následujícímu:

1. Pokud virtuální počítač používá spravované disky, vytvoří se disketa repliky s `-ASRReplica` příponou. `-ASRReplica`Kopie se používají pro replikaci.
1. Pokud virtuální počítač používá nespravované disky, vytvoří se na cílovém účtu úložiště disketa repliky.
1. Všechny disky se zkopírují z oblasti převzetí služeb při selhání do nové cílové oblasti.
1. Po dokončení synchronizace se spustí rozdílová replikace a v souladu se zásadami replikace se vytvoří bod obnovení.

#### <a name="estimated-time-to-do-the-reprotection"></a>Odhadovaný čas k provedení ochrany

Ve většině případů Azure Site Recovery nereplikuje úplná data do zdrojové oblasti.
Následující podmínky určují, kolik dat se replikuje:

1. Pokud dojde k odstranění, poškození nebo nepřístupné zdrojovému virtuálnímu počítači z nějakého důvodu, třeba ke změně nebo odstranění skupiny prostředků, dojde během ochrany k úplné počáteční replikaci k tomu, že ve zdrojové oblasti nejsou k dispozici žádná data, která by bylo možné použít.
1. Pokud jsou data zdrojového virtuálního počítače přístupná, vypočítávají se jenom rozdílové rozdíly porovnáním disků a pak přenesených. Odhadovaný čas získáte v tabulce níže.

|Příklad situace | Doba, jakou trvalo opětovné zapnutí ochrany |
|---|---|
|Zdrojová oblast má 1 virtuální počítač s 1 TB standardního disku.<br/>Používá se jenom 127 GB dat a zbytek disku je prázdný.<br/>Typ disku je Standard s propustností 60 MB/s.<br/>Po převzetí služeb při selhání se žádná data nezmění.| Přibližná doba: 60-90 minut.<br/> Během ochrany Site Recovery naplní kontrolní součet všech dat. To funguje na 45MBps, takže celková doba, kterou bude trvat, je 127 GB/45 MB/s, přibližně 45 minut.<br/>Site Recovery k automatickému škálování je potřeba určitý čas režie, přibližně 20-30 minut. |
|Zdrojová oblast má 1 virtuální počítač s 1 TB standardního disku.<br/>Používá se jenom 127 GB dat a zbytek disku je prázdný.<br/>Typ disku je Standard s propustností 60 MB/s.<br/>po převzetí služeb při selhání se změní 45 GB dat.| Přibližná doba: 2,5-3 hodiny.<br/> Během ochrany Site Recovery naplní kontrolní součet všech dat. To funguje na 45MBps, takže celková doba, kterou bude trvat, je 127 GB/45 MB/s, přibližně 45 minut.<br/>Rychlost přenosu je přibližně 16% propustnosti nebo 9.6 MB/s. Proto se doba přenosu aplikuje na změny 45 GB, což je 45 GB/9.6 MB/s, 80 přibližně v minutách.<br/>Site Recovery k automatickému škálování je potřeba určitý čas režie, přibližně 20-30 minut. |
|Zdrojová oblast má 1 virtuální počítač s 1 TB standardního disku.<br/>Používá se jenom 20 GB dat a zbytek disku je prázdný.<br/>Typ disku je Standard s propustností 60 MB/s.<br/>Počáteční data na disku hned po převzetí služeb při selhání byly 15 GB. Po převzetí služeb při selhání se změnila 5 GB dat. Celková naplněná data jsou tedy 20 GB.| Přibližná doba: 1 – 1,5 hodiny<br/>Vzhledem k tomu, že data naplněná na disku jsou menší než 10% velikosti disku, provádíme kompletní počáteční replikaci.<br/> Rychlost přenosu je přibližně 16% propustnosti nebo 9.6 MB/s. Proto se doba přenosu aplikuje na změny 20 GB, což je 20 GB/9.6 MB/s, přibližně 36 minut.<br/>Site Recovery k automatickému škálování je potřeba určitý čas režie, přibližně 20-30 minut. |
|Zdrojová oblast má 1 virtuální počítač s 1 TB Premium diskem.<br/>Používá se jenom 127 GB dat a zbytek disku je prázdný.<br/>Typ disku je Premium s propustností 200 MB/s.<br/>Po převzetí služeb při selhání se žádná data nezmění.| Přibližná doba: 2 hodiny.<br/>Během ochrany Site Recovery naplní kontrolní součet všech dat. To funguje na 25MBps (až 16% propustnosti disku), takže celková doba, kterou bude trvat, je 127 GB/25 MB/s, přibližně 87 minut.<br/>Site Recovery k automatickému škálování je potřeba určitý čas režie, přibližně 20-30 minut. |
|Zdrojová oblast má 1 virtuální počítač s 1 TB Premium diskem.<br/>Používá se jenom 127 GB dat a zbytek disku je prázdný.<br/>Typ disku je Premium s propustností 200 MB/s.<br/>po převzetí služeb při selhání se změní 45 GB dat.| Přibližná doba: 2,5-3 hodiny.<br/>Během ochrany Site Recovery naplní kontrolní součet všech dat. To funguje na 25MBps (až 16% propustnosti disku), takže celková doba, kterou bude trvat, je 127 GB/25 MB/s, přibližně 87 minut.</br>Rychlost přenosu je přibližně 16% propustnosti nebo 32MBps. Proto se doba přenosu aplikuje na změny 45 GB, které jsou 45 GB/32 MB/s, přibližně 24 minut.<br/>Site Recovery k automatickému škálování je potřeba určitý čas režie, přibližně 20-30 minut. |
|Zdrojová oblast má 1 virtuální počítač s 1 TB Premium diskem.<br/>Používá se jenom 20 GB dat a zbytek disku je prázdný.<br/>Typ disku je Premium s propustností 200 MB/s.<br/>Počáteční data na disku hned po převzetí služeb při selhání byly 15 GB. Po převzetí služeb při selhání se změnila 5 GB dat. Celková naplněná data jsou tedy 20 GB.| Přibližná doba: 30-45 minut.<br/>Vzhledem k tomu, že data naplněná na disku jsou menší než 10% velikosti disku, provádíme kompletní počáteční replikaci.<br/>Rychlost přenosu je přibližně 16% propustnosti nebo 32MBps. Proto se doba přenosu aplikuje na změny 20 GB, což je 20 GB/32 MB/s, přibližně 11 minut.<br/>Site Recovery k automatickému škálování vyžaduje určitý čas režie, přibližně 20-30 minut. |

Pokud je virtuální počítač po převzetí služeb při selhání zpátky do primární oblasti (tj. Pokud je virtuální počítač znovu chráněný z primární oblasti na oblast zotavení po havárii), odstraní se cílový virtuální počítač a přidružené síťové karty.

Když je virtuální počítač znovu chráněn z oblasti zotavení po havárii do primární oblasti, neodstraníme primární virtuální počítač Erstwhile a přidružené síťové karty (y).

## <a name="next-steps"></a>Další kroky

Po uzamknutí virtuálního počítače můžete iniciovat převzetí služeb při selhání. Převzetí služeb při selhání vypne virtuální počítač v sekundární oblasti a vytvoří a spustí virtuální počítač v primární oblasti, přičemž během tohoto procesu dojde k krátkému výpadku. Pro tento proces doporučujeme zvolit vhodný čas a před zahájením úplného převzetí služeb při selhání do primární lokality spustit testovací převzetí služeb při selhání. [Další informace](site-recovery-failover.md) o převzetí služeb při selhání Azure Site Recovery.
