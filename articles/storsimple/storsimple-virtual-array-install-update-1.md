---
title: Instalace aktualizace 1.0 na virtuálním poli StorSimple | Dokumenty společnosti Microsoft
description: Popisuje, jak pomocí webového uživatelského uživatelského prostředí StorSimple Virtual Array použít aktualizace pomocí portálu Azure a metody opravy hotfix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fa53213e577028628d48db91704578e23888f2a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254505"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Instalace aktualizace 1.0 do virtuálního pole StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci aktualizace 1.0 do virtuálního pole StorSimple prostřednictvím místního webového uživatelského prostředí a prostřednictvím portálu Azure.

Aktualizace softwaru nebo opravy hotfix použijete, aby vaše virtuální pole StorSimple bylo aktuální. Před aktualizací doporučujeme nejprve převzít svazky nebo sdílené složky do offline na hostiteli a potom na zařízení. Minimalizuje se tak možnost poškození dat. Po offline svazky nebo sdílené složky byste měli také provést ruční zálohování zařízení.

> [!IMPORTANT]
> - Aktualizace 1.0 odpovídá verzi softwaru **10.0.10296.0** v zařízení. Informace o novince v této aktualizaci naleznete v [poznámkách k verzi aktualizace 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Nezapomeňte, že instalace aktualizace nebo opravy hotfix restartuje zařízení. Vzhledem k tomu, že virtuální pole StorSimple je zařízení s jedním uzly, je přerušeno jakékoli probíhající vstupně-up a prostoje zařízení.
>
> - Aktualizace 1 je dostupná na webu Azure Portal jenom v případě, že virtuální pole používá aktualizaci 0.6. U virtuálních polí se spuštěnou verzí před aktualizací 0.6 je třeba nejprve nainstalovat aktualizaci 0.6 a poté nainstalovat aktualizaci 1.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud se sem schovali aktualizace 0.2 a novější, doporučujeme nainstalovat aktualizace prostřednictvím portálu Azure. Postup portálu vyžaduje, aby uživatel prohledává, stahuje a poté aktualizuje aktualizace. V závislosti na verzi softwaru, kterou virtuální pole používá, se použití aktualizace prostřednictvím portálu Azure liší.

 - Pokud vaše virtuální pole používá aktualizaci 0.6, portál Azure přímo nainstaluje aktualizaci 1 (10.0.10296.0) do vašeho zařízení. Tento postup trvá přibližně 7 minut.
 - Pokud virtuální pole běží verze před aktualizací 0.6, aktualizace se provádí ve dvou fázích. Portál Azure nejprve nainstaluje aktualizaci 0.6 (10.0.10293.0) do vašeho zařízení. Virtuální pole se restartuje a portál pak nainstaluje aktualizaci 1 (10.0.10296.0) do vašeho zařízení. Tento postup trvá přibližně 15 minut.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Po dokončení instalace přejděte do služby Správce zařízení StorSimple. Vyberte **Zařízení** a pak vyberte a klikněte na zařízení, které jste právě aktualizovali. Přejděte **na nastavení > Správa aktualizací > zařízení**. Zobrazená verze softwaru by měla být **10.0.10296.0**.

![Verze softwaru po aktualizaci](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského prostředí

Při použití místního webového uživatelského prostředí existují dva kroky:

* Stažení aktualizace nebo opravy hotfix
* Instalace aktualizace nebo opravy hotfix

> [!IMPORTANT] 
> **Pokračujte v této aktualizaci pouze v případě, že používáte aktualizaci 0.6 (10.0.10293.0). Pokud používáte starší verzi, nejprve nainstalujte do zařízení [aktualizaci 0.6](storsimple-virtual-array-install-update-06.md) a potom nainstalujte aktualizaci 1.**

### <a name="download-the-update-or-the-hotfix"></a>Stažení aktualizace nebo opravy hotfix

Pokud ve virtuálním poli běží aktualizace 0.6, stáhněte aktualizaci 1 z katalogu Microsoft Update následujícím postupem.

#### <a name="to-download-the-update-or-the-hotfix"></a>Stažení aktualizace nebo opravy hotfix

1. Spusťte aplikaci [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Internet Explorer a přejděte na .

2. Pokud v tomto počítači používáte katalog Microsoft Update poprvé, klepněte po zobrazení výzvy k instalaci doplňku Katalogu microsoft update na **tlačítko Nainstalovat.**

3. Do vyhledávacího pole katalogu Microsoft Update zadejte číslo opravy hotfix, kterou chcete stáhnout, do znalostní báze Knowledge Base (KB). Zadejte **hodnotu 4047203** pro aktualizaci 1.0 a klepněte na tlačítko **Hledat**.
   
    Zobrazí se výpis opravy hotfix, například **Aktualizace virtuálního pole StorSimple 1.0**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Klepněte na tlačítko **Stáhnout**.

5. Stáhněte si dva soubory do složky. Složku můžete také zkopírovat do sdílené síťové složky, která je dostupná ze zařízení.

6. Otevřete složku, ve které jsou soubory umístěny.

    ![Soubory v balíčku](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Zobrazí se dva soubory:
    -  Soubor `WindowsTH-KB3011067-x64`samostatného balíčku společnosti Microsoft Update . Tento soubor se používá k aktualizaci softwaru zařízení.
    - Soubor obsahující kumulativní aktualizace `windows8.1-kb4034681-x64`pro srpen . Další informace o tom, co je součástí této kumulativní, naleznete v [srpnu měsíční kumulativní zabezpečení](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

Před instalací aktualizace nebo opravy hotfix se ujistěte, že:

 - Aktualizace nebo oprava hotfix jsou staženy místně na hostiteli nebo jsou přístupné prostřednictvím sdílené síťové složky.
 - Ve virtuálním poli je spuštěna aktualizace 0.6 (10.0.10293.0). Pokud používáte verzi před aktualizací 0.6, [nainstalujte nejprve aktualizaci 0.6](storsimple-virtual-array-install-update-06.md) a poté nainstalujte aktualizaci 1.

Tento postup trvá přibližně 4 minuty. Chcete-li nainstalovat aktualizaci nebo opravu hotfix, proveďte následující kroky.

#### <a name="to-install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

1. V místním webovém uživatelském prostředí přejděte na**stránku Aktualizace softwaru** **údržby** > . Poznamenejte si verzi softwaru, kterou používáte. **Pokračujte v této aktualizaci pouze v případě, že používáte aktualizaci 0.6 (10.0.10293.0). Pokud používáte starší verzi, nejprve nainstalujte do zařízení [aktualizaci 0.6](storsimple-virtual-array-install-update-06.md) a potom nainstalujte aktualizaci 1.**
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. V **části Aktualizovat cestu k souboru**zadejte název souboru aktualizace nebo opravy hotfix. Můžete také přejít na instalační soubor aktualizace nebo opravy hotfix, pokud je umístěn do sdílené síťové složky. Klikněte na **Použít**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Zobrazí se upozornění. Vzhledem k tomu, virtuální pole je jeden uzel zařízení, po instalaci aktualizace, zařízení restartuje a je prostoje. Klikněte na ikonu šeku.
   
   ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Aktualizace se spustí. Po úspěšné aktualizaci zařízení se restartuje. Místní uI není přístupné v této době trvání.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Po dokončení restartování se dostanete na **přihlašovací** stránku. Chcete-li ověřit, zda byl software zařízení aktualizován, přejděte v místním webovém uživatelském rozhraní na**stránku** **Maintenance** > Software Update . Zobrazená verze softwaru by měla být **10.0.0.0.0.10296** pro aktualizaci 1.0.
   
   > [!NOTE]
   > Verze softwaru hlásíme trochu jiným způsobem v místním webovém uživatelském prostředí a na portálu Azure. Například místní webové uživatelské prostředí hlásí **10.0.0.0.0.10296** a portál Azure hlásí **10.0.10296.0** pro stejnou verzi.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Opakováním kroků 2-4 nainstalujte opravu `windows8.1-kb4012213-x64`zabezpečení systému Windows pomocí souboru . Virtuální pole se restartuje po instalaci a je třeba se přihlásit do místního webového uživatelského prostředí.

> [!NOTE]
> Pokud jste přímo použili aktualizaci 1 na zařízení s verzí před aktualizací 0.6, chybí vám některé aktualizace. Další kroky vám poskytne podpora společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

Další informace o [správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).
