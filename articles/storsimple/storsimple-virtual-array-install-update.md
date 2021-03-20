---
title: Instalace aktualizací ve virtuálním poli Microsoft Azure StorSimple | Microsoft Docs
description: Popisuje, jak použít webové uživatelské rozhraní StorSimple Virtual Array k instalaci aktualizací pomocí portálu a metody oprav hotfix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 9997a97b-9382-43ed-b56e-61369335c987
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3bd6f298ad2bb01503492b52c2d50dec82ec0ca5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94534037"
---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>Instalace aktualizací do virtuálního pole StorSimple – Azure Portal

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci aktualizací ve virtuálním poli StorSimple prostřednictvím místního webového uživatelského rozhraní a prostřednictvím Azure Portal. Chcete-li, aby vaše virtuální pole StorSimple bylo aktuální, je třeba použít aktualizace softwaru nebo opravy hotfix. 

Mějte na paměti, že při instalaci aktualizace nebo opravy hotfix dojde k restartování zařízení. Vzhledem k tom, že virtuální pole StorSimple je zařízení s jedním uzlem, je přerušeno jakékoli vstupně-výstupní operace a dojde k výpadku zařízení. 

Než použijete aktualizaci, doporučujeme, abyste nejprve převzali svazky nebo sdílené složky na hostiteli a pak zařízení. Minimalizuje se tak možnost poškození dat.

> [!IMPORTANT]
> Pokud používáte Software Update 0,1 nebo GA, je nutné použít metodu hotfix prostřednictvím místního webového uživatelského rozhraní k instalaci aktualizace 0,3. Pokud používáte aktualizaci 0,2, doporučujeme nainstalovat aktualizace prostřednictvím portálu Azure Classic.
 

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky při použití místního webového uživatelského rozhraní:

* Stáhnout aktualizaci nebo opravu hotfix
* Instalace aktualizace nebo opravy hotfix

### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizaci nebo opravu hotfix

Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Stažení aktualizace nebo opravy hotfix

1. Spusťte Internet Explorer a přejděte na [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Pokud na tomto počítači používáte Katalog služby Microsoft Update poprvé, po zobrazení výzvy k instalaci doplňku Katalog služby Microsoft Update klikněte na **Nainstalovat**.

3. Do vyhledávacího pole katalogu Microsoft Update zadejte číslo znalostní báze (KB), kterou chcete stáhnout. Zadejte **3182061** pro aktualizaci 0,3 a pak klikněte na **Hledat**.
   
    Zobrazí se seznam oprav hotfix, například **StorSimple Virtual Array Update 0,3**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update/download1.png)

4. Klikněte na **Přidat**. Aktualizace se přidá do košíku.

5. Klikněte na **Zobrazit košík**.

6. Klikněte na **Stáhnout**. Zadejte místní umístění, do kterého chcete aktualizace stáhnout, nebo do něj přejděte pomocí tlačítka **Procházet**. Aktualizace se stáhnou do zadaného umístění do podsložky se stejným názvem, jako má aktualizace. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.

7. Otevřete zkopírovanou složku, měl by se zobrazit soubor samostatného balíčku Microsoft Update `WindowsTH-KB3011067-x64` . Tento soubor se používá k instalaci aktualizace nebo opravy hotfix.

### <a name="install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

Před instalací aktualizace nebo oprav hotfix se ujistěte, že máte aktualizaci nebo opravu hotfix staženou místně na hostiteli nebo přístupnou přes sdílenou síťovou složku. 

Tuto metodu použijte, chcete-li nainstalovat aktualizace na zařízení s verzí softwaru GA nebo Update 0,1. Dokončení tohoto postupu trvá méně než 2 minuty. Provedením následujících kroků nainstalujete aktualizaci nebo opravu hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

1. V místním webovém uživatelském rozhraní přejdete na **Údržba**  >  **aktualizace softwaru**.
   
    ![Snímek obrazovky se zobrazí aktualizace softwaru vybrané z nabídky Údržba.](./media/storsimple-virtual-array-install-update/update1m.png)

2. Do pole **cesta k souboru aktualizace** zadejte název souboru aktualizace nebo opravy hotfix. Můžete také přejít k instalačnímu souboru aktualizace nebo opravy hotfix, pokud je umístěn ve sdílené síťové složce. Klikněte na **Použít**.
   
    ![Snímek obrazovky se zobrazí textové pole cesta k souboru aktualizace na stránce aktualizace softwaru.](./media/storsimple-virtual-array-install-update/update2m.png)

3. Zobrazí se upozornění. Vzhledem k tomu, že se jedná o zařízení s jedním uzlem, po použití aktualizace se zařízení restartuje a dojde k výpadku. Klikněte na ikonu zaškrtněte.
   
   ![Snímek obrazovky se zobrazí dialogové okno s upozorněním na výpadky.](./media/storsimple-virtual-array-install-update/update3m.png)

4. Spustí se aktualizace. Po úspěšné aktualizaci zařízení se restartuje. Místní uživatelské rozhraní není v tuto dobu k dispozici.
   
    ![Snímek obrazovky ukazuje zprávu o úspěchu aktualizace.](./media/storsimple-virtual-array-install-update/update5m.png)

5. Po dokončení restartování přejdete na **přihlašovací** stránku. Chcete-li ověřit, zda byl software zařízení aktualizován, v místním webovém uživatelském rozhraní, navštivte web  >  **aktualizace softwaru** údržba. Zobrazená verze softwaru by měla být **10.0.0.0.0.10288.0** pro aktualizaci 0,3.
   
   > [!NOTE]
   > Verze softwaru oznamujeme trochu jiným způsobem v místním webovém uživatelském rozhraní a Azure Portal. Například místní webové uživatelské rozhraní hlásí **10.0.0.0.0.10288** a sestavy Azure Portal **10.0.10288.0** pro stejnou verzi.
   
    ![Snímek obrazovky s aktuální verzí softwaru zobrazí stránku aktualizace softwaru.](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud používáte aktualizaci 0,2, doporučujeme nainstalovat aktualizace prostřednictvím Azure Portal. Procedura portálu vyžaduje, aby uživatel kontroloval, stáhl a nainstaloval aktualizace. Dokončení tohoto postupu trvá přibližně 7 minut. Provedením následujících kroků nainstalujete aktualizaci nebo opravu hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

Až se instalace dokončí (podle stavu úlohy v 100%), přejdete do služby StorSimple Správce zařízení. Vyberte **zařízení** a potom v seznamu zařízení připojených k této službě klikněte na zařízení, které chcete aktualizovat. V okně **Nastavení** vyberte Přejít do části **Spravovat** a vyberte **aktualizace zařízení**. Zobrazená verze softwaru by měla být **10.0.10288.0**.


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).

