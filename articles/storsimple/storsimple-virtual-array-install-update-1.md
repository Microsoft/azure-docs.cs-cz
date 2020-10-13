---
title: Instalace aktualizace 1,0 ve virtuálním poli StorSimple | Microsoft Docs
description: Popisuje, jak použít webové uživatelské rozhraní StorSimple Virtual Array k použití aktualizace 1,0 s použitím metody Azure Portal a Hot Fix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 4bcd0f6d4d1ac505598c839f89850f9b55a9c36a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977489"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Instalace aktualizace 1,0 ve virtuálním poli StorSimple

## <a name="overview"></a>Overview

Tento článek popisuje kroky potřebné k instalaci aktualizace 1,0 ve virtuálním poli StorSimple prostřednictvím místního webového uživatelského rozhraní a prostřednictvím Azure Portal.

Aktualizace softwaru nebo opravy hotfix můžete použít, chcete-li, aby vaše virtuální pole StorSimple zůstalo v aktuálním stavu. Než použijete aktualizaci, doporučujeme, abyste nejprve převzali svazky nebo sdílené složky na hostiteli a pak zařízení. Minimalizuje se tak možnost poškození dat. Až budou svazky nebo sdílené složky offline, měli byste také provést ruční zálohování zařízení.

> [!IMPORTANT]
>
> - Aktualizace 1,0 odpovídá verzi **10.0.10296.0** softwaru na vašem zařízení. Informace o tom, co je v této aktualizaci nové, najdete v [poznámkách k verzi pro aktualizaci 1,0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Mějte na paměti, že při instalaci aktualizace nebo opravy hotfix dojde k restartování zařízení. Vzhledem k tom, že virtuální pole StorSimple je zařízení s jedním uzlem, je přerušeno jakékoli vstupně-výstupní operace a dojde k výpadku zařízení.
>
> - Aktualizace 1 je dostupná v Azure Portal jenom v případě, že je ve virtuálním poli spuštěná aktualizace 0,6. Pro virtuální pole, na kterých běží předběžná aktualizace 0,6 verze, musíte nejdřív nainstalovat Update 0,6 a pak nainstalovat Update 1.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud používáte aktualizaci 0,2 a novější, doporučujeme nainstalovat aktualizace prostřednictvím Azure Portal. Procedura portálu vyžaduje, aby uživatel kontroloval, stáhl a nainstaloval aktualizace. V závislosti na verzi softwaru, kterou vaše virtuální pole běží, se použití aktualizace prostřednictvím Azure Portal liší.

 - Pokud je ve vašem virtuálním poli spuštěná aktualizace 0,6, Azure Portal na vaše zařízení přímo nainstaluje Update 1 (10.0.10296.0). Dokončení tohoto postupu trvá přibližně 7 minut.
 - Pokud je ve virtuálním poli spuštěná verze před aktualizací 0,6, aktualizace se provádí ve dvou fázích. Azure Portal na vaše zařízení nejdřív nainstaluje aktualizaci 0,6 (10.0.10293.0). Virtuální pole se restartuje a portál pak na svém zařízení nainstaluje Update 1 (10.0.10296.0). Dokončení tohoto postupu trvá přibližně 15 minut.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Po dokončení instalace přejdete do služby StorSimple Device Manager. Vyberte **zařízení** a pak vyberte a klikněte na zařízení, které jste právě aktualizovali. Přejít na **nastavení > spravovat > aktualizace zařízení**. Zobrazená verze softwaru by měla být **10.0.10296.0**.

![Verze softwaru po aktualizaci](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky při použití místního webového uživatelského rozhraní:

* Stáhnout aktualizaci nebo opravu hotfix
* Instalace aktualizace nebo opravy hotfix

> [!IMPORTANT] 
> **Pokračujte v této aktualizaci pouze v případě, že používáte aktualizaci 0,6 (10.0.10293.0). Pokud používáte starší verzi, nainstalujte nejdřív na zařízení [aktualizaci 0,6](storsimple-virtual-array-install-update-06.md) a pak použijte Update 1.**

### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizaci nebo opravu hotfix

Pokud je ve vašem virtuálním poli spuštěná aktualizace 0,6, Stáhněte aktualizaci 1 z katalogu Microsoft Update provedením následujících kroků.

#### <a name="to-download-the-update-or-the-hotfix"></a>Stažení aktualizace nebo opravy hotfix

1. Spusťte Internet Explorer a přejděte na [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Pokud na tomto počítači používáte katalog Microsoft Update poprvé, po zobrazení výzvy k instalaci doplňku katalogu Microsoft Update klikněte na **nainstalovat** .

3. Do vyhledávacího pole katalogu Microsoft Update zadejte číslo znalostní báze (KB), kterou chcete stáhnout. Zadejte **4047203** pro aktualizaci 1,0 a pak klikněte na **Hledat**.
   
    Zobrazí se seznam oprav hotfix, například **StorSimple Virtual Array Update 1,0**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Klikněte na tlačítko **Stáhnout**.

5. Stáhněte si dva soubory do složky. Můžete také zkopírovat složku do síťové sdílené složky, která je dosažitelná ze zařízení.

6. Otevřete složku, ve které jsou umístěny soubory.

    ![Soubory v balíčku](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Zobrazí se dva soubory:
    -  Soubor samostatného balíčku Microsoft Update `WindowsTH-KB3011067-x64` . Tento soubor slouží k aktualizaci softwaru zařízení.
    - Soubor, který obsahuje kumulativní aktualizace pro srpen `windows8.1-kb4034681-x64` . Další informace o tom, co je zahrnuté v této kumulativní aktualizaci, najdete v [měsíčních souhrnech zabezpečení](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

Před instalací aktualizace nebo oprav hotfix se ujistěte, že:

 - Máte aktualizaci nebo opravu hotfix staženou místně na svém hostiteli nebo přístupnou přes sdílenou síťovou složku.
 - Ve virtuálním poli je spuštěná aktualizace 0,6 (10.0.10293.0). Pokud používáte verzi před aktualizací 0,6, [nainstalujte nejdřív update 0,6](storsimple-virtual-array-install-update-06.md) a pak nainstalujte Update 1.

Dokončení této procedury trvá přibližně 4 minuty. Provedením následujících kroků nainstalujete aktualizaci nebo opravu hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

1. V místním webovém uživatelském rozhraní přejdete na **Údržba**  >  **aktualizace softwaru**. Poznamenejte si verzi softwaru, kterou používáte. **Pokračujte v této aktualizaci pouze v případě, že používáte aktualizaci 0,6 (10.0.10293.0). Pokud používáte starší verzi, nainstalujte nejdřív na zařízení [aktualizaci 0,6](storsimple-virtual-array-install-update-06.md) a pak použijte Update 1.**
   
    ![aktualizace zařízení 1](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. Do pole **cesta k souboru aktualizace**zadejte název souboru aktualizace nebo opravy hotfix. Můžete také přejít k instalačnímu souboru aktualizace nebo opravy hotfix, pokud je umístěn ve sdílené síťové složce. Klikněte na **Použít**.
   
    ![aktualizace zařízení 2](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Zobrazí se upozornění. Vzhledem k tom, že virtuální pole je zařízení s jedním uzlem, po použití aktualizace se zařízení restartuje a dojde k výpadku. Klikněte na ikonu zaškrtněte.
   
   ![aktualizace zařízení 3](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Spustí se aktualizace. Po úspěšné aktualizaci zařízení se restartuje. Místní uživatelské rozhraní není v tuto dobu k dispozici.
   
    ![aktualizace zařízení 4](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Po dokončení restartování přejdete na **přihlašovací** stránku. Chcete-li ověřit, zda byl software zařízení aktualizován, v místním webovém uživatelském rozhraní, **Maintenance**navštivte web  >  **aktualizace softwaru**údržba. Zobrazená verze softwaru by měla být **10.0.0.0.0.10296** pro aktualizaci 1,0.
   
   > [!NOTE]
   > Verze softwaru oznamujeme trochu jiným způsobem v místním webovém uživatelském rozhraní a Azure Portal. Například místní webové uživatelské rozhraní hlásí **10.0.0.0.0.10296** a sestavy Azure Portal **10.0.10296.0** pro stejnou verzi.
   
    ![aktualizace zařízení 5](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Opakujte kroky 2-4 pro instalaci opravy zabezpečení systému Windows pomocí souboru `windows8.1-kb4012213-x64` . Virtuální pole se po instalaci restartuje a musíte se přihlásit k místnímu webovému uživatelskému rozhraní.

> [!NOTE]
> Pokud jste přímo použili aktualizaci Update 1 na zařízení, na kterém je spuštěná verze před aktualizací 0,6, chybí některé aktualizace. Kontaktujte prosím podpora Microsoftu pro další kroky.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).
