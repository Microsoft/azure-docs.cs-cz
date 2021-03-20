---
title: Instalace aktualizace 0,5 ve virtuálním poli StorSimple | Microsoft Docs
description: Popisuje, jak použít webové uživatelské rozhraní StorSimple Virtual Array k použití aktualizace 0,5 s použitím metody Azure Portal a Hot Fix.
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
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: 5723e8d9fc7b0a72393dda1b225ca073a6474a0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94534309"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Instalace aktualizace 0,5 ve virtuálním poli StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci aktualizace 0,5 ve virtuálním poli StorSimple prostřednictvím místního webového uživatelského rozhraní a prostřednictvím Azure Portal. Chcete-li, aby vaše virtuální pole StorSimple bylo aktuální, je třeba použít aktualizace softwaru nebo opravy hotfix.

Než použijete aktualizaci, doporučujeme, abyste nejprve převzali svazky nebo sdílené složky na hostiteli a pak zařízení. Minimalizuje se tak možnost poškození dat. Až budou svazky nebo sdílené složky offline, měli byste také provést ruční zálohování zařízení.

> [!IMPORTANT]
> - Aktualizace 0,5 odpovídá verzi **10.0.10290.0** softwaru na vašem zařízení. Informace o tom, co je v této aktualizaci nové, najdete v [poznámkách k verzi pro aktualizaci 0,5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Pokud používáte aktualizaci 0,2 nebo novější, doporučujeme nainstalovat aktualizace prostřednictvím Azure Portal. Pokud používáte Software Update 0,1 nebo GA, je nutné použít metodu hotfix prostřednictvím místního webového uživatelského rozhraní k instalaci aktualizace 0,5.
>
> - Mějte na paměti, že při instalaci aktualizace nebo opravy hotfix dojde k restartování zařízení. Vzhledem k tom, že virtuální pole StorSimple je zařízení s jedním uzlem, je přerušeno jakékoli vstupně-výstupní operace a dojde k výpadku zařízení.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud používáte aktualizaci 0,2 a novější, doporučujeme nainstalovat aktualizace prostřednictvím Azure Portal. Procedura portálu vyžaduje, aby uživatel kontroloval, stáhl a nainstaloval aktualizace. Dokončení tohoto postupu trvá přibližně 7 minut. Provedením následujících kroků nainstalujete aktualizaci nebo opravu hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po dokončení instalace přejdete do služby StorSimple Správce zařízení. Vyberte **zařízení** a pak vyberte a klikněte na zařízení, které jste právě aktualizovali. Přejít na **nastavení > spravovat > aktualizace zařízení**. Zobrazená verze softwaru by měla být **10.0.10290.0**.

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky při použití místního webového uživatelského rozhraní:

* Stáhnout aktualizaci nebo opravu hotfix
* Instalace aktualizace nebo opravy hotfix

### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizaci nebo opravu hotfix

Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Stažení aktualizace nebo opravy hotfix

1. Spusťte Internet Explorer a přejděte na [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Pokud na tomto počítači používáte Katalog služby Microsoft Update poprvé, po zobrazení výzvy k instalaci doplňku Katalog služby Microsoft Update klikněte na **Nainstalovat**.

3. Do vyhledávacího pole katalogu Microsoft Update zadejte číslo znalostní báze (KB), kterou chcete stáhnout. Zadejte **4021576** pro aktualizaci 0,5 a pak klikněte na **Hledat**.
   
    Zobrazí se seznam oprav hotfix, například **StorSimple Virtual Array Update 0,5**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Klikněte na **Stáhnout**. 

5. Měli byste vidět dva soubory ke stažení, soubor *. msu* a *. cab* . Každý z těchto souborů si stáhněte do složky. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.

6. Otevřete složku, ve které jsou umístěny soubory.
    ![Soubory v balíčku](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    Zobrazí se následující:
    -  Soubor samostatného balíčku Microsoft Update `WindowsTH-KB3011067-x64` . Tento soubor slouží k aktualizaci softwaru zařízení.
    - Soubor balíčku agenta monitorování v Ženevě `GenevaMonitoringAgentPackageInstaller` . Tento soubor slouží k aktualizaci agenta služby pro monitorování a diagnostiku (MDS). Dvakrát klikněte na soubor CAB. Zobrazí se soubor. msi. Vyberte soubor, klikněte na něj pravým tlačítkem a potom soubor **rozbalte** . K aktualizaci agenta použijete soubor _. msi_ .

        ![Extrahovat soubor aktualizace agenta služby MDS](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

Před instalací aktualizace nebo oprav hotfix se ujistěte, že máte aktualizaci nebo opravu hotfix staženou místně na hostiteli nebo přístupnou přes sdílenou síťovou složku.

Tuto metodu použijte, chcete-li nainstalovat aktualizace na zařízení s verzí softwaru GA nebo Update 0,1. Dokončení tohoto postupu trvá méně než 2 minuty. Provedením následujících kroků nainstalujete aktualizaci nebo opravu hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

1. V místním webovém uživatelském rozhraní přejdete na **Údržba**  >  **aktualizace softwaru**.
   
    ![Snímek obrazovky se zobrazí aktualizace softwaru vybrané z nabídky Údržba.](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. Do pole **cesta k souboru aktualizace** zadejte název souboru aktualizace nebo opravy hotfix. Můžete také přejít k instalačnímu souboru aktualizace nebo opravy hotfix, pokud je umístěn ve sdílené síťové složce. Klikněte na **Použít**.
   
    ![Snímek obrazovky se zobrazí textové pole cesta k souboru aktualizace na stránce aktualizace softwaru.](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Zobrazí se upozornění. Vzhledem k tomu, že se jedná o zařízení s jedním uzlem, po použití aktualizace se zařízení restartuje a dojde k výpadku. Klikněte na ikonu zaškrtněte.
   
   ![Snímek obrazovky se zobrazí dialogové okno s upozorněním na výpadky.](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Spustí se aktualizace. Po úspěšné aktualizaci zařízení se restartuje. Místní uživatelské rozhraní není v tuto dobu k dispozici.
   
    ![Snímek obrazovky ukazuje zprávu o úspěchu aktualizace.](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Po dokončení restartování přejdete na **přihlašovací** stránku. Chcete-li ověřit, zda byl software zařízení aktualizován, v místním webovém uživatelském rozhraní, navštivte web  >  **aktualizace softwaru** údržba. Zobrazená verze softwaru by měla být **10.0.0.0.0.10290.0** pro aktualizaci 0,5.
   
   > [!NOTE]
   > Verze softwaru oznamujeme trochu jiným způsobem v místním webovém uživatelském rozhraní a Azure Portal. Například místní webové uživatelské rozhraní hlásí **10.0.0.0.0.10290** a sestavy Azure Portal **10.0.10290.0** pro stejnou verzi.
   
    ![Snímek obrazovky s aktuální verzí softwaru zobrazí stránku aktualizace softwaru.](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. Dalším krokem je aktualizace agenta služby MDS. Na stránce **aktualizace softwaru** přejděte na **cestu k souboru aktualizace** a přejděte k `GenevaMonitoringAgentPackageInstaller.msi` souboru. Opakujte kroky 2-4. Po restartování virtuálního pole se přihlaste k místnímu webovému uživatelskému rozhraní.

Aktualizace je nyní dokončena.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).

