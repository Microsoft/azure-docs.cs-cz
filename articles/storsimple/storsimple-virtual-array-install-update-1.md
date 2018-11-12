---
title: Instalace Update 1.0 na StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje způsob použití StorSimple Virtual Array webového uživatelského rozhraní aktualizací pomocí Azure portal a opravy hotfix – metoda
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
ms.openlocfilehash: 34439268163b4da6ac45af09101f4f609de7e9ee
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248159"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Instalace Update 1.0 na StorSimple Virtual Array

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci Update 1.0 na StorSimple Virtual Array prostřednictvím místního webového uživatelského rozhraní a prostřednictvím webu Azure portal.

Použití aktualizací softwaru nebo oprav hotfix k udržování aktuálnosti StorSimple Virtual Array. Před instalací aktualizace, doporučujeme vám věnovat svazky nebo sdílené složky offline na hostiteli první a pak zařízení. Minimalizuje se tak možnost poškození dat. Jakmile svazky nebo sdílené složky jsou offline, byste měli také provést ruční zálohování zařízení.

> [!IMPORTANT]
> - Update 1.0 odpovídá **10.0.10296.0** verze softwaru na vašem zařízení. Informace o novinkách v této aktualizaci najdete v části [zpráva k vydání verze Update 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Mějte na paměti, který instaluje aktualizaci nebo opravu hotfix restartuje vaše zařízení. Vzhledem k tomu, že StorSimple Virtual Array se zařízením s jedním uzlem, naruší se všechny vstupně-výstupních operací v průběhu a vaše zařízení dojde k výpadku.
>
> - Aktualizace 1 je k dispozici na webu Azure Portal, pouze pokud virtuální pole je spuštěn aktualizace 0.6. Pro virtuální pole s verzemi 0,6 před aktualizací musíte nejprve nainstalovat aktualizace 0.6 a nainstalujte aktualizaci 1.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud aktualizace s 0,2 nebo novějším, doporučujeme, abyste instalaci aktualizace na webu Azure portal. Portálu postup vyžaduje, aby uživatel kontrolovat, stáhněte si a nainstalujte aktualizace. V závislosti na verzi softwaru, které běží vaše virtuální pole, použití aktualizací prostřednictvím webu Azure portal se liší.

 - Pokud vaše virtuální pole běží aktualizace 0.6, na webu Azure portal nainstaluje Update 1 (10.0.10296.0) přímo na vašem zařízení. Tento postup trvá asi 7 minut na dokončení.
 - Pokud vaše virtuální pole je spuštěna s verzí starší než aktualizace 0.6, aktualizace se provádí ve dvou fází. Na webu Azure portal nejdřív nainstaluje aktualizace 0.6 (10.0.10293.0) na vašem zařízení. Restartuje virtuální pole a Update 1 (10.0.10296.0) na portálu potom nainstaluje na zařízení. Tento postup trvá přibližně 15 minut.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Po dokončení instalace přejděte do služby Správce zařízení StorSimple. Vyberte **zařízení** a potom klikněte na zařízení, které jste právě aktualizovali. Přejděte na **Nastavení > Správa > aktualizace zařízení**. Zobrazené software verze by měla být **10.0.10296.0**.

![Verze softwaru po aktualizaci](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky, při použití místního webového uživatelského rozhraní:

* Stáhnout aktualizaci nebo opravu hotfix
* Nainstalujte aktualizaci nebo opravu hotfix

> [!IMPORTANT] 
> **Pokračujte v této aktualizaci pouze v případě, že spustíte aktualizace 0.6 (10.0.10293.0). Pokud používáte starší verzi, [instalace aktualizace 0.6](storsimple-virtual-array-install-update-06.md) na vašem zařízení první a pak použít Update 1.**

### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizaci nebo opravu hotfix

Pokud vaše virtuální pole běží aktualizace 0.6, postupujte následovně Chcete-li stáhnout Update 1 z katalogu služby Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Chcete-li stáhnout aktualizace nebo opravy hotfix

1. Spustit aplikaci Internet Explorer a přejděte do [ http://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Pokud používáte katalog služby Microsoft Update na tomto počítači poprvé, klikněte na tlačítko **nainstalovat** po zobrazení výzvy k instalaci doplňku katalog služby Microsoft Update.

3. Do vyhledávacího pole katalogu služby Microsoft Update zadejte číslo znalostní báze Knowledge Base (KB) opravy hotfix, které chcete stáhnout. Zadejte **4047203** aktualizaci 1.0 a potom klikněte na tlačítko **hledání**.
   
    Zobrazí se výpis opravy hotfix, například **StorSimple Virtual Array Update 1.0**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Klikněte na **Stáhnout**.

5. Stáhněte si příslušné dva soubory do složky. Můžete také zkopírovat složku do sdílené síťové složky dostupné ze zařízení.

6. Otevřete složku, ve kterém se soubory nacházejí.

    ![Soubory v balíčku](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Zobrazí se dva soubory:
    -  Microsoft Update samostatného balíčku souboru `WindowsTH-KB3011067-x64`. Tento soubor slouží k aktualizaci softwaru zařízení.
    - Soubor, který obsahuje kumulativní aktualizace ze srpna `windows8.1-kb4034681-x64`. Další informace o co je zahrnuté v této kumulativní, přejděte na [měsíční kumulativní srpna](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Nainstalujte aktualizaci nebo opravu hotfix

Před instalací aktualizace nebo opravy hotfix Ujistěte se, že:

 - Máte aktualizaci nebo opravu hotfix stáhnout místně na vašem hostiteli nebo přístupné přes síťové sdílené složky.
 - Vaše virtuální pole je spuštěná aktualizace 0.6 (10.0.10293.0). Pokud používáte verzi nižší než aktualizace 0.6, [instalace aktualizace 0.6](storsimple-virtual-array-install-update-06.md) první a nainstalujte aktualizaci 1.

Tento postup trvá přibližně 4 minuty. Proveďte následující kroky k instalaci, aktualizaci nebo opravu hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>K instalaci aktualizace nebo opravy hotfix

1. V místním webovém uživatelském rozhraní, přejděte na **údržby** > **aktualizace softwaru**. Poznamenejte si verzi softwaru, kterou používáte. **Pokračujte v této aktualizaci pouze v případě, že spustíte aktualizace 0.6 (10.0.10293.0). Pokud používáte starší verzi, [instalace aktualizace 0.6](storsimple-virtual-array-install-update-06.md) na vašem zařízení první a pak použít Update 1.**
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. V **cesta k souboru aktualizace**, zadejte název souboru pro aktualizaci nebo opravu hotfix. Můžete také přejít k instalačnímu souboru aktualizace nebo opravy hotfix Pokud umístěn ve sdílené síťové složce. Klikněte na tlačítko **Použít**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Zobrazí se upozornění. Poté, co tuto aktualizaci nenainstalujete, zařízení se restartuje a nedochází k výpadkům je uvedený virtuální pole zařízením s jedním uzlem. Kliknutím na ikonu zaškrtnutí.
   
   ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Spustí se aktualizace. Po úspěšné aktualizaci zařízení restartuje. Místní uživatelské rozhraní není dostupný v této hodnotě duration.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Po dokončení restartování, budete přesměrováni na **přihlášení** stránky. Chcete-li ověřit, že zařízení má aktualizovat, v místním webovém uživatelském rozhraní, přejděte na **údržby** > **aktualizace softwaru**. Zobrazené software verze by měla být **10.0.0.0.0.10296** pro Update 1.0.
   
   > [!NOTE]
   > V trochu jinak v místním webovém uživatelském rozhraní a webu Azure portal vytvoříme sestavy verzí softwaru. Například místního webového uživatelského rozhraní sestavy **10.0.0.0.0.10296** a Azure portal sestavy **10.0.10296.0** pro stejnou verzi.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Opakujte kroky 2 až 4 nainstalujte opravu zabezpečení Windows pomocí souboru `windows8.1-kb4012213-x64`. Po instalaci restartuje virtuální pole a potřebujete se přihlásit do místního webového uživatelského rozhraní.

> [!NOTE]
> Pokud přímo použít aktualizaci 1 pro zařízení s verzí starší než aktualizace 0.6, chybí některé aktualizace. Obraťte se prosím Microsoft Support pro další kroky.

## <a name="next-steps"></a>Další postup

Další informace o [Správa StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
