---
title: Znovu nastavte ochranu virtuálních počítačů Azure do primární oblasti pomocí Azure Site Recovery | Microsoft Docs
description: Popisuje, jak znovu chránit virtuální počítače Azure po převzetí služeb při selhání, sekundární do primární oblasti, pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 955e1b84f897a5eb877033e0a58b8d661f143a14
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954193"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Opětovné zapnutí ochrany virtuálních počítačů Azure v primární oblasti


Při [převzetí služeb při selhání](site-recovery-failover.md) virtuálních počítačů Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md)se virtuální počítače spustí v sekundární oblasti v nechráněném stavu. Pokud dojde k selhání back-VM u virtuálních počítačů do primární oblasti, je třeba provést následující akce:

- Znovu nastavte ochranu virtuálních počítačů v sekundární oblasti, aby se začaly replikovat do primární oblasti.
- Po dokončení ochrany a replikaci virtuálních počítačů můžete převzít služby při selhání z sekundární do primární oblasti.

## <a name="prerequisites"></a>Požadavky
1. Převzetí služeb virtuálního počítače při selhání z primární do sekundární oblasti musí být potvrzené.
2. Primární cílový webový server by měl být dostupný a měl by být přístupný nebo vytvářet prostředky v této oblasti.

## <a name="reprotect-a-vm"></a>Opětovné zapnutí ochrany virtuálního počítače

1. V **trezoru** > **replikované položky**klikněte pravým tlačítkem myši na virtuální počítač převzetí služeb při selhání a vyberte **znovu zapnout ochranu**. Směr další ochrany by se měl zobrazit ze sekundárního na primární.

   ![Znovunastavení ochrany](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Zkontrolujte skupinu prostředků, síť, úložiště a skupiny dostupnosti. Pak klikněte na **OK**. Pokud jsou nějaké prostředky označené jako nové, vytvoří se v rámci procesu nové ochrany.
3. V rámci úlohy ochrany se v cílové lokalitě dokončí nejnovější data. Po dokončení dojde k rozdílové replikaci. Pak můžete převzít služby při selhání zpět do primární lokality. Pomocí možnosti přizpůsobit můžete vybrat účet úložiště nebo síť, kterou chcete použít při opětovném zapnutí ochrany.

   ![Přizpůsobení možnosti](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Přizpůsobení nastavení opětovné ochrany

Během ochrany můžete přizpůsobit následující vlastnosti cílové VMe.

![Přizpůsobení](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Vlastnost |Poznámky:  |
|---------|---------|
|Cílová skupina prostředků     | Upravte cílovou skupinu prostředků, ve které se virtuální počítač vytvoří. V rámci ochrany je cílový virtuální počítač odstraněný. Můžete zvolit novou skupinu prostředků, ve které se po převzetí služeb při selhání vytvoří virtuální počítač.        |
|Cílová virtuální síť     | Cílovou síť nelze změnit během úlohy opětovného zapnutí ochrany. Chcete-li změnit síť, proveďte znovu mapování sítě.         |
|Cílové úložiště (sekundární virtuální počítač nepoužívá spravované disky)     | Můžete změnit účet úložiště, který virtuální počítač používá po převzetí služeb při selhání.         |
|Spravované disky repliky (sekundární virtuální počítač používá spravované disky)    | Site Recovery vytvoří repliku spravované disky v primární oblasti pro zrcadlení spravovaných disků sekundárního virtuálního počítače.         |
|Úložiště mezipaměti     | Můžete zadat účet úložiště mezipaměti, který se použije při replikaci. Ve výchozím nastavení se vytvoří nový účet úložiště mezipaměti, pokud neexistuje.         |
|Skupina dostupnosti     |Pokud je virtuální počítač v sekundární oblasti součástí skupiny dostupnosti, můžete zvolit skupinu dostupnosti pro cílový virtuální počítač v primární oblasti. Ve výchozím nastavení se Site Recovery pokusí najít existující skupinu dostupnosti v primární oblasti a použít ji. Během přizpůsobení můžete zadat novou skupinu dostupnosti.         |


### <a name="what-happens-during-reprotection"></a>Co se stane během ochrany?

Ve výchozím nastavení dojde k následujícímu:

1. Účet úložiště mezipaměti se vytvoří v oblasti, ve které běží virtuální počítač pro převzetí služeb při selhání.
2. Pokud cílový účet úložiště (původní účet úložiště v primární oblasti) neexistuje, vytvoří se nový. Přiřazený název účtu úložiště je název účtu úložiště používaného sekundárním virtuálním počítačem, který je s příponou ASR.
3. Pokud virtuální počítač používá spravované disky, v primární oblasti se vytvoří replika spravované disky pro ukládání dat replikovaných z disků sekundárního virtuálního počítače.
4. Pokud cílová skupina dostupnosti neexistuje, vytvoří se nová v rámci úlohy opětovného ochrany v případě potřeby. Pokud jste upravili nastavení ochrany, použije se vybraná sada.

Když aktivujete úlohu opětovné ochrany a cílový virtuální počítač existuje, dojde k následujícímu:

1. Cílový virtuální počítač je vypnutý, pokud je spuštěný.
2. Pokud virtuální počítač používá spravované disky, vytvoří se kopie původních disků s příponou-ASRReplica. Původní disky se odstraní. Kopie-ASRReplica se používají pro replikaci.
3. Pokud virtuální počítač používá nespravované disky, jsou datové disky cílového virtuálního počítače odpojeny a použity pro replikaci. Na virtuálním počítači se vytvoří a připojí kopie disku s operačním systémem. Původní disk s operačním systémem je odpojený a používá se k replikaci.
4. Synchronizují se jenom změny mezi zdrojovým diskem a cílovým diskem. Rozdíly jsou vypočítány porovnáním disků a pak přenesených. Chcete-li zjistit odhadovanou dobu kontroly níže.
5. Po dokončení synchronizace se spustí rozdílová replikace a v souladu se zásadami replikace vytvoří bod obnovení.

Když aktivujete úlohu opětovné ochrany a cílový virtuální počítač a disky neexistují, dojde k následujícímu:
1. Pokud virtuální počítač používá spravované disky, vytvoří se disketa repliky s příponou-ASRReplica. Kopie-ASRReplica se používají pro replikaci.
2. Pokud virtuální počítač používá nespravované disky, vytvoří se na cílovém účtu úložiště disketa repliky.
3. Všechny disky se zkopírují z oblasti převzetí služeb při selhání do nové cílové oblasti.
4. Po dokončení synchronizace se spustí rozdílová replikace a v souladu se zásadami replikace vytvoří bod obnovení.

#### <a name="estimated-time--to-do-the-reprotection"></a>Odhadovaný čas k provedení ochrany 

Ve většině případů Azure Site Recovery nereplikuje úplná data do zdrojové oblasti. Níže jsou uvedené podmínky, které určují, kolik dat se bude replikovat:

1.  Pokud se zdrojová data virtuálních počítačů odstraňují, jsou poškozená nebo nepřístupná z nějakého důvodu, jako je změna nebo odstranění skupiny prostředků během ochrany, dojde k tomu, že ve zdrojové oblasti nejsou k dispozici žádná data, která by bylo možné použít.
2.  Pokud jsou k dispozici data zdrojového virtuálního počítače, vypočítávají se jenom rozdíly, které porovnávají oba disky a pak se přenesou. Pokud chcete získat odhadovaný čas, podívejte se na následující tabulku. 

|\* * Příklad situace * * | \* * Čas potřebný k opětovnému zapnutí ochrany * * |
|--- | --- |
|Zdrojová oblast má 1 virtuální počítač s 1 TB standardní diskovou službou.<br/>– Používají se jenom 127 GB dat a zbytek disku je prázdný.<br/>– Typ disku je Standard s propustností 60 MiB/S.<br/>– Po převzetí služeb při selhání se nezmění žádná data| Přibližná doba 45 minut – 1,5 hodin<br/> – Během opětovné ochrany Site Recovery naplní kontrolní součet celých dat, který bude trvat 127 GB/45 MB ~ 45 minut<br/>– Pro Site Recovery automatické škálování, které je 20-30 minut, se vyžaduje určitý čas režie.<br/>– Žádné poplatky za výstup |
|Zdrojová oblast má 1 virtuální počítač s 1 TB standardní diskovou službou.<br/>– Používají se jenom 127 GB dat a zbytek disku je prázdný.<br/>– Typ disku je Standard s propustností 60 MiB/S.<br/>-45 GB změny dat po převzetí služeb při selhání| Přibližná doba 1 hodiny – 2 hodiny<br/>– Během opětovné ochrany Site Recovery naplní kontrolní součet celých dat, který bude trvat 127 GB/45 MB ~ 45 minut<br/>-Doba přenosu pro uplatnění změn 45 GB, což je 45 GB/45 MB/s ~ 17 minut<br/>– Poplatky za výstupní data by byly jenom 45 GB dat, která nejsou pro kontrolní součet.|
 



## <a name="next-steps"></a>Další kroky

Po uzamknutí virtuálního počítače můžete iniciovat převzetí služeb při selhání. Převzetí služeb při selhání vypne virtuální počítač v sekundární oblasti a vytvoří a spustí virtuální počítač v primární oblasti s malým výpadkem. Doporučujeme, abyste si odpovídajícím způsobem zvolili čas a před spuštěním úplného převzetí služeb při selhání do primární lokality spustíte testovací převzetí služeb při selhání. [Další informace](site-recovery-failover.md) o převzetí služeb při selhání.
