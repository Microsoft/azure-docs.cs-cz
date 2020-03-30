---
title: Opětovné přizabezpečení virtuálních počítačů Azure k primární oblasti pomocí Azure Site Recovery | Dokumenty společnosti Microsoft
description: Popisuje, jak znovu chránit virtuální počítače Azure po převzetí služeb při selhání, sekundární k primární oblasti pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 73747b8331054cdc3bfa1f4073ccf2cdb62ab326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283238"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Opětovné nahrazení služby Azure virtuálními počítači do primární oblasti

Když [si pojistit virtuální](site-recovery-failover.md) počítače Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md), virtuální počítače spouštět v sekundární oblasti, v **nechráněném** stavu. Pokud chcete navrácení služeb po službách pro virtuální chod do primární oblasti, proveďte následující úkoly:

1. Znovu chránit virtuální chodvvvviše v sekundární oblasti tak, aby se začaly replikovat do primární oblasti.
1. Po dokončení opětovnéochrany a virtuální počítače jsou replikovat, můžete převzetí služeb při selhání ze sekundární do primární oblasti.

## <a name="prerequisites"></a>Požadavky

- Převzetí služeb při selhání virtuálního zařízení z primární do sekundární oblasti musí být potvrzena.
- Primární cílová lokalita by měla být k dispozici a měli byste mít přístup nebo vytvořit prostředky v této oblasti.

## <a name="reprotect-a-vm"></a>Opětovné přizamknení virtuálního mísa

1. V**replikovaných položkách** **úložiště** > klepněte pravým tlačítkem myši na neúspěšný virtuální virtuální virtuální ms a vyberte **příkaz Znovu chránit**. Směr reprotection by měl být zobrazil od sekundárního k primárnímu.

   ![Znovu chránit](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Zkontrolujte skupinu prostředků, síť, úložiště a skupiny dostupnosti. Pak klikněte na **OK**. Pokud jsou všechny prostředky označeny jako nové, jsou vytvořeny jako součást procesu opětovnéochrany.
1. Úloha opětovné ochrany zasílá cílový web nejnovějšími daty. Po dokončení úlohy dojde k rozdílové replikaci. Potom můžete převzetí služeb při selhání zpět do primární lokality. Pomocí možnosti přizpůsobit můžete vybrat účet úložiště nebo síť, kterou chcete použít při opětovném přichytvám.

   ![Přizpůsobit, možnost](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Přizpůsobení nastavení opětovné ochrany

Můžete přizpůsobit následující vlastnosti cílového virtuálního počítače během opětovnéochrany.

![Přizpůsobení](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Vlastnost |Poznámky  |
|---------|---------|
|Cílová skupina prostředků | Upravte cílovou skupinu prostředků, ve které se vytvoří virtuální hod. Jako součást opětovné ochrany se odstraní cílový virtuální virtuální virtuální ms. Můžete zvolit novou skupinu prostředků, pod kterou chcete vytvořit virtuální hod po převzetí služeb při selhání. |
|Cílová virtuální síť | Cílovou síť nelze během úlohy opětovného ochrany změnit. Chcete-li změnit síť, znovu mapování sítě. |
|Cílové úložiště (sekundární virtuální počítače nepoužívají spravované disky) | Účet úložiště, který virtuální hod používá po převzetí služeb při selhání. |
|Replika spravovaných disků (sekundární virtuální počítače používají spravované disky) | Site Recovery vytvoří repliky spravované disky v primární oblasti zrcadlit sekundární virtuálního počítače spravované disky. |
|Úložiště mezipaměti | Můžete zadat účet úložiště mezipaměti, který se má použít během replikace. Ve výchozím nastavení je vytvořen nový účet úložiště mezipaměti, pokud neexistuje. |
|Skupina dostupnosti | Pokud je virtuální virtuální hotel v sekundární oblasti součástí skupiny dostupnosti, můžete zvolit sadu dostupnosti pro cílový virtuální virtuální ms v primární oblasti. Ve výchozím nastavení se site recovery pokusí najít existující sadu dostupnosti v primární oblasti a použít ji. Během vlastního nastavení můžete zadat novou sadu dostupnosti. |

### <a name="what-happens-during-reprotection"></a>Co se stane během opětovné ochrany?

Ve výchozím nastavení dochází k následujícímu:

1. Účet úložiště mezipaměti se vytvoří v oblasti, kde je spuštěn virtuální ms a u kterých se nezdařilo.
1. Pokud cílový účet úložiště (původní účet úložiště v primární oblasti) neexistuje, vytvoří se nový účet. Název účtu přiřazeného úložiště je název účtu úložiště, který používá sekundární virtuální `asr`virtuální_
1. Pokud váš virtuální počítač používá spravované disky, repliky spravované disky se vytvoří v primární oblasti pro uložení dat replikovaných z disků sekundárního virtuálního počítače.
1. Pokud cílová dostupnost neexistuje, nová je vytvořena jako součást úlohy opětovného ochrany, pokud je to nutné. Pokud jste přizpůsobili nastavení opětovné ochrany, použije se vybraná sada.

Když spustíte úlohu opětovného ochrany a cílový virtuální virtuální byl, dojde k následujícímu:

1. Virtuální virtuální hotel na cílové straně je vypnutý, pokud je spuštěný.
1. Pokud virtuální modul používá spravované disky, vytvoří se kopie `-ASRReplica` původního disku s příponou. Původní disky budou odstraněny. Kopie `-ASRReplica` se používají pro replikaci.
1. Pokud virtuální modul používá nespravované disky, jsou datové disky cílového virtuálního počítače odpojené a používané pro replikaci. Kopie disku operačního systému se vytvoří a připojí k virtuálnímu počítači. Původní disk operačního systému je odpojen a používán pro replikaci.
1. Synchronizují se pouze změny mezi zdrojovým a cílovým diskem. Rozdíly jsou vypočítány porovnáním obou disků a pak převedeny. Zkontrolujte níže, abyste našli odhadovaný čas dokončení opětovné ochrany.
1. Po dokončení synchronizace začíná rozdílová replikace a bod obnovení je vytvořen v souladu se zásadami replikace.

Když spustíte úlohu opětovného ochrany a cílový virtuální modul a disky neexistují, dojde k následujícímu:

1. Pokud virtuální modul používá spravované disky, repliky `-ASRReplica` disky jsou vytvořeny s příponou. Kopie `-ASRReplica` se používají pro replikaci.
1. Pokud virtuální modul používá nespravované disky, repliky disky jsou vytvořeny v účtu cílového úložiště.
1. Celé disky jsou zkopírovány z oblasti převzetí štěstí do nové cílové oblasti.
1. Po dokončení synchronizace začíná rozdílová replikace a bod obnovení je vytvořen v souladu se zásadami replikace.

#### <a name="estimated-time-to-do-the-reprotection"></a>Odhadovaný čas na reprotekci

Ve většině případů Azure Site Recovery nereplikuje úplná data do zdrojové oblasti.
Následující podmínky určují, kolik dat je replikováno:

1. Pokud jsou zdrojová data virtuálního počítače z nějakého důvodu odstraněná, poškozená nebo nepřístupná, například změna nebo odstranění skupiny prostředků, dojde během opětovné ochrany k úplné počáteční replikaci, protože ve zdrojové oblasti nejsou k dispozici žádná data.
1. Pokud jsou přístupná zdrojová data virtuálního počítače, pak se vypočítají pouze rozdíly porovnáním obou disků a pak přeneseny. Chcete-li získat odhadovaný čas, podívejte se do níže uvedené tabulky.

|Příklad situace | Doba, která byla doba na opětovné naochranu |
|---|---|
|Oblast zdroje má 1 virtuální virtuální počítače se standardním diskem o velikosti 1 TB.<br/>Používá se pouze 127 GB dat a zbytek disku je prázdný.<br/>Typ disku je standardní s propustností 60 MiB/S.<br/>Po převzetí služeb při selhání se žádná změna dat nemění.| Přibližný čas: 45 minut – 1,5 hodiny.<br/>Během opětovné ochrany site recovery naplní kontrolní součet všech dat, která budou trvat 127 GB / 45 GB, přibližně 45 minut.<br/>Některé režijní čas je vyžadován pro obnovení webu na automatické škálování, přibližně 20-30 minut.<br/>Žádná obvinění z výstupu. |
|Oblast zdroje má 1 virtuální virtuální počítače se standardním diskem o velikosti 1 TB.<br/>Používá se pouze 127 GB dat a zbytek disku je prázdný.<br/>Typ disku je standardní s propustností 60 MiB/S.<br/>45 GB dat se změní po převzetí služeb při selhání.| Přibližný čas: 1 hodina – 2 hodiny.<br/>Během opětovné ochrany site recovery naplní kontrolní součet všech dat, která budou trvat 127 GB / 45 GB, přibližně 45 minut.<br/>Doba přenosu pro použití změn 45 GB, což je 45 GB/ 45 MB/s, přibližně 17 minut.<br/>Poplatky za odchozí přenos dat by byly pro změny dat o velikosti 45 GB, nikoli pro kontrolní součet. |

Když je virtuální počítač znovu chráněný po selhání zpět do primární oblasti (tj. pokud je virtuální počítač znovu chráněný z primární oblasti do oblasti zotavení po Havárii), odstraní se cílový virtuální počítač a přidružené nic (y) cílové.

Když je virtuální počítač znovu chráněný z oblasti ZOTAVENÍ do primární oblasti, neodstraníme někdejší primární virtuální počítač a přidružené nic(s).

## <a name="next-steps"></a>Další kroky

Po zamknuté virtuálního počítače můžete zahájit převzetí služeb při selhání. Převzetí služeb při selhání vypne virtuální ho v sekundární oblasti a vytvoří a spustí virtuální ho v primární oblasti, s krátkými prostoji během tohoto procesu. Doporučujeme zvolit vhodný čas pro tento proces a spustit test převzetí služeb při selhání před zahájením úplné převzetí služeb při selhání do primární lokality. [Další informace](site-recovery-failover.md) o převzetí služeb při selhání azure site recovery.
