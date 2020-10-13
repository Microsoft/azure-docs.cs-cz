---
title: Instalace aktualizace 0,6 ve virtuálním poli StorSimple | Microsoft Docs
description: Popisuje, jak použít webové uživatelské rozhraní StorSimple Virtual Array k použití aktualizace 0,6 s použitím metody Azure Portal a Hot Fix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 33563d12ac31679eb3b658e7625d95581988536d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977472"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Instalace aktualizace 0,6 ve virtuálním poli StorSimple

## <a name="overview"></a>Overview

Tento článek popisuje kroky potřebné k instalaci aktualizace 0,6 ve virtuálním poli StorSimple prostřednictvím místního webového uživatelského rozhraní a prostřednictvím Azure Portal. Aktualizace softwaru nebo opravy hotfix můžete použít, chcete-li, aby vaše virtuální pole StorSimple zůstalo v aktuálním stavu.

Než použijete aktualizaci, doporučujeme, abyste nejprve převzali svazky nebo sdílené složky na hostiteli a pak zařízení. Minimalizuje se tak možnost poškození dat. Až budou svazky nebo sdílené složky offline, měli byste také provést ruční zálohování zařízení.

> [!IMPORTANT]
>
> - Aktualizace 0,6 odpovídá verzi **10.0.10293.0** softwaru na vašem zařízení. Informace o tom, co je v této aktualizaci nové, najdete v [poznámkách k verzi pro aktualizaci 0,6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Pokud používáte aktualizaci 0,2 nebo novější, doporučujeme nainstalovat aktualizace prostřednictvím Azure Portal. Pokud používáte Software Update 0,1 nebo GA, je nutné použít metodu hotfix prostřednictvím místního webového uživatelského rozhraní k instalaci aktualizace 0,6.
>
> - Mějte na paměti, že při instalaci aktualizace nebo opravy hotfix dojde k restartování zařízení. Vzhledem k tom, že virtuální pole StorSimple je zařízení s jedním uzlem, je přerušeno jakékoli vstupně-výstupní operace a dojde k výpadku zařízení.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud používáte aktualizaci 0,2 a novější, doporučujeme nainstalovat aktualizace prostřednictvím Azure Portal. Procedura portálu vyžaduje, aby uživatel kontroloval, stáhl a nainstaloval aktualizace. Dokončení tohoto postupu trvá přibližně 7 minut. Provedením následujících kroků nainstalujete aktualizaci nebo opravu hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po dokončení instalace přejdete do služby StorSimple Device Manager. Vyberte **zařízení** a pak vyberte a klikněte na zařízení, které jste právě aktualizovali. Přejít na **nastavení > spravovat > aktualizace zařízení**. Zobrazená verze softwaru by měla být **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky při použití místního webového uživatelského rozhraní:

* Stáhnout aktualizaci nebo opravu hotfix
* Instalace aktualizace nebo opravy hotfix

### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizaci nebo opravu hotfix

Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Stažení aktualizace nebo opravy hotfix

1. Spusťte Internet Explorer a přejděte na [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Pokud na tomto počítači používáte katalog Microsoft Update poprvé, po zobrazení výzvy k instalaci doplňku katalogu Microsoft Update klikněte na **nainstalovat** .

3. Do vyhledávacího pole katalogu Microsoft Update zadejte číslo znalostní báze (KB), kterou chcete stáhnout. Zadejte **4023268** pro aktualizaci 0,6 a pak klikněte na **Hledat**.
   
    Zobrazí se seznam oprav hotfix, například **StorSimple Virtual Array Update 0,6**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Klikněte na tlačítko **Stáhnout**.

5. Mělo by se zobrazit pět souborů ke stažení. Každý z těchto souborů si stáhněte do složky. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.

6. Otevřete složku, ve které jsou umístěny soubory.
    ![Soubory v balíčku](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Zobrazí se následující:
    -  Soubor samostatného balíčku Microsoft Update `WindowsTH-KB3011067-x64` . Tento soubor slouží k aktualizaci softwaru zařízení.
    - Soubor balíčku agenta monitorování v Ženevě `GenevaMonitoringAgentPackageInstaller` . Tento soubor slouží k aktualizaci agenta služby pro monitorování a diagnostiku (MDS). Dvakrát klikněte na soubor CAB. Zobrazí se soubor _. msi_ . Vyberte soubor, klikněte na něj pravým tlačítkem a potom soubor **rozbalte** . Pomocí souboru _. msi_ aktualizujete agenta.

        ![Extrahovat soubor aktualizace agenta služby MDS](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Pokud používáte StorSimple Update 0,5 (0.0.10293.0), nemusíte aktualizovat agenta služby MDS.

    - Tři soubory, které obsahují důležité aktualizace zabezpečení systému Windows,, `windows8.1-kb4012213-x64` `windows8.1-kb3205400-x64` a `windows8.1-kb4019213-x64` .


### <a name="install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

Před instalací aktualizace nebo oprav hotfix se ujistěte, že máte aktualizaci nebo opravu hotfix staženou místně na hostiteli nebo přístupnou přes sdílenou síťovou složku.

Tuto metodu použijte, chcete-li nainstalovat aktualizace na zařízení s verzí softwaru GA nebo Update 0,1. Dokončení tohoto postupu trvá přibližně 12 minut. Provedením následujících kroků nainstalujete aktualizaci nebo opravu hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

1. V místním webovém uživatelském rozhraní přejdete na **Údržba**  >  **aktualizace softwaru**. Poznamenejte si verzi softwaru, kterou používáte. Pokud používáte **10.0.10290.0**, nemusíte v kroku 6 aktualizovat agenta služby MDS.
   
    ![aktualizace zařízení 1](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. Do pole **cesta k souboru aktualizace**zadejte název souboru aktualizace nebo opravy hotfix. Můžete také přejít k instalačnímu souboru aktualizace nebo opravy hotfix, pokud je umístěn ve sdílené síťové složce. Klikněte na **Použít**.
   
    ![aktualizace zařízení 2](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Zobrazí se upozornění. Vzhledem k tom, že virtuální pole je zařízení s jedním uzlem, po použití aktualizace se zařízení restartuje a dojde k výpadku. Klikněte na ikonu zaškrtněte.
   
   ![aktualizace zařízení 3](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Spustí se aktualizace. Po úspěšné aktualizaci zařízení se restartuje. Místní uživatelské rozhraní není v tuto dobu k dispozici.
   
    ![aktualizace zařízení 4](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Po dokončení restartování přejdete na **přihlašovací** stránku. Chcete-li ověřit, zda byl software zařízení aktualizován, v místním webovém uživatelském rozhraní, **Maintenance**navštivte web  >  **aktualizace softwaru**údržba. Zobrazená verze softwaru by měla být **10.0.0.0.0.10293** pro aktualizaci 0,6.
   
   > [!NOTE]
   > Verze softwaru oznamujeme trochu jiným způsobem v místním webovém uživatelském rozhraní a Azure Portal. Například místní webové uživatelské rozhraní hlásí **10.0.0.0.0.10293** a sestavy Azure Portal **10.0.10293.0** pro stejnou verzi.
   
    ![aktualizace zařízení 5](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Tento krok přeskočte, pokud jste před instalací této aktualizace spustili StorSimple Virtual Array Update 0,5 (**10.0.10290.0**). Verzi softwaru jste si poznamenali v kroku 1 před zahájením aktualizace. Pokud jste spustili aktualizaci 0,5, Váš agent služby MDS už je aktuální.

    Pokud používáte verzi softwaru před aktualizací 0,5, je dalším krokem pro aktualizaci agenta služby MDS. Na stránce **aktualizace softwaru** přejděte na **cestu k souboru aktualizace** a přejděte k `GenevaMonitoringAgentPackageInstaller.msi` souboru. Opakujte kroky 2-4. Po restartování virtuálního pole se přihlaste k místnímu webovému uživatelskému rozhraní.

7. Opakujte krok 2-4 pro instalaci oprav zabezpečení systému Windows pomocí souborů `windows8.1-kb4012213-x64` , `windows8.1-kb3205400-x64` , a `windows8.1-kb4019213-x64` . Virtuální pole se po každé instalaci restartuje a musíte se přihlásit k místnímu webovému uživatelskému rozhraní.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).

