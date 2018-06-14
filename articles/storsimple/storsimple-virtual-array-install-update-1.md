---
title: Instalaci aktualizace 1.0 v poli virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje způsob použití pole virtuální zařízení StorSimple webového uživatelského rozhraní na aktualizace pomocí metody Azure portal a opravy hotfix
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
ms.openlocfilehash: a85290f3f56eb1e1bf57524c43c6d4fea36129f7
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2017
ms.locfileid: "24012075"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Nainstalujte aktualizace 1.0 na pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci aktualizace 1.0 na pole virtuální zařízení StorSimple prostřednictvím místního webového uživatelského rozhraní a prostřednictvím portálu Azure.

Můžete použít aktualizace softwaru nebo opravy hotfix, které k zachování aktualizovaného stavu pole virtuální zařízení StorSimple. Než použijete aktualizaci, doporučujeme vám věnovat svazky nebo sdílené složky do offline režimu na hostiteli první a pak zařízení. Tím se minimalizují možnost poškození dat. Jakmile svazky nebo sdílené složky jsou offline, můžete také provést ruční zálohování zařízení.

> [!IMPORTANT]
> - Aktualizace 1.0 odpovídá **10.0.10296.0** verze softwaru ve vašem zařízení. Informace na to, co je nového v této aktualizaci najdete na [poznámky k verzi pro aktualizaci 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Mějte na paměti, který instaluje aktualizaci nebo opravu hotfix restartuje vaše zařízení. Vzhledem k tomu, že pole virtuální zařízení StorSimple je jeden uzel zařízení, dojde k narušení všechny vstupně-výstupních operací v průběhu a zařízení dojde k výpadku.
>
> - Aktualizace 1 je k dispozici na webu Azure portal pouze v případě, že virtuální pole běží aktualizace 0,6. Pro virtuální pole 0,6 verzemi před aktualizací musíte nejprve nainstalovat aktualizace 0,6 a pak nainstalujte Update 1.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud aktualizace 0,2 a novější, doporučujeme instalovat aktualizace prostřednictvím portálu Azure. Portál postup vyžaduje, aby uživatel ke kontrole, stáhněte a nainstalujte aktualizace. V závislosti na verzi softwaru, který je spuštěn virtuální pole, použití aktualizace prostřednictvím portálu Azure se liší.

 - Pokud vaše virtuální pole běží aktualizace 0,6, portálu Azure přímo nainstaluje Update 1 (10.0.10296.0) ve vašem zařízení. Tento postup trvá asi 7 minut.
 - Pokud vaše virtuální pole běží na verzi před aktualizací 0,6, aktualizace se provádí v dvě fáze. Portál Azure nejprve nainstaluje aktualizaci 0,6 (10.0.10293.0) ve vašem zařízení. Restartuje virtuální pole a portálu pak nainstaluje aktualizaci 1 (10.0.10296.0) ve vašem zařízení. Tento postup trvá asi 15 minut.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Po dokončení instalace, přejděte do služby StorSimple Manager zařízení. Vyberte **zařízení** a poté vyberte a klikněte na zařízení k aktualizaci. Přejděte na **Nastavení > Správa > aktualizace zařízení**. Zobrazit software verze musí být **10.0.10296.0**.

![Verze softwaru po aktualizaci](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky při použití místního webového uživatelského rozhraní:

* Stáhnout aktualizace nebo opravy hotfix
* Instalovat aktualizace nebo opravy hotfix

> [!IMPORTANT] 
> **Pokračujte s touto aktualizací pouze v případě, že používáte verzi Update 0,6 (10.0.10293.0). Pokud používáte starší verzi, [nainstalujte aktualizaci 0,6](storsimple-virtual-array-install-update-06.md) na vašem zařízení první a potom použijte Update 1.**

### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizace nebo opravy hotfix

Pokud vaše virtuální pole běží aktualizace 0,6, proveďte následující kroky můžete stáhnout z katalogu služby Microsoft Update Update 1.

#### <a name="to-download-the-update-or-the-hotfix"></a>Chcete-li stáhnout aktualizace nebo opravy hotfix

1. Spusťte Internet Explorer a přejděte na [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Pokud používáte katalogu služby Microsoft Update na tomto počítači poprvé, klikněte na tlačítko **nainstalovat** po zobrazení výzvy k instalaci doplňku katalogu služby Microsoft Update.

3. Do vyhledávacího pole katalogu služby Microsoft Update zadejte číslo znalostní báze Knowledge Base (KB) opravy hotfix, které chcete stáhnout. Zadejte **4047203** pro aktualizaci 1.0 a pak klikněte na **vyhledávání**.
   
    Zobrazí se seznam oprav hotfix, například **zařízení StorSimple virtuální pole Update 1.0**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Klikněte na **Stáhnout**.

5. Stáhněte dva soubory do složky. Také můžete zkopírovat složku do sdílené síťové složky, který je dosažitelný z tohoto zařízení.

6. Otevřete složku, kde jsou umístěny soubory.

    ![Soubory v balíčku](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Zobrazí dva soubory:
    -  Soubor balíčku samostatné aktualizace Microsoft `WindowsTH-KB3011067-x64`. Tento soubor se používá k aktualizaci softwaru zařízení.
    - Soubor, který obsahuje kumulativní aktualizace srpen `windows8.1-kb4034681-x64`. Další informace o součástech v této kumulativní, přejděte na [měsíční souhrn zabezpečení srpen](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Instalovat aktualizace nebo opravy hotfix

Před instalací aktualizace nebo opravy hotfix Ujistěte se, že:

 - Máte aktualizaci nebo opravu hotfix stáhnout místně na vašem hostiteli nebo zpřístupněno sdílené síťové složky.
 - Vaše virtuální pole je spuštěna aktualizace 0,6 (10.0.10293.0). Pokud používáte verzi před aktualizací 0,6, [nainstalujte aktualizaci 0,6](storsimple-virtual-array-install-update-06.md) první a pak nainstalujte Update 1.

Tento postup trvá přibližně 4 minuty. Proveďte následující kroky k instalaci, aktualizaci nebo opravu hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>K instalaci aktualizace nebo opravy hotfix

1. V místní webového uživatelského rozhraní, přejděte do **údržby** > **aktualizace softwaru**. Poznamenejte si verzi softwaru, kterou používáte. **Pokračujte s touto aktualizací pouze v případě, že používáte verzi Update 0,6 (10.0.10293.0). Pokud používáte starší verzi, [nainstalujte aktualizaci 0,6](storsimple-virtual-array-install-update-06.md) na vašem zařízení první a potom použijte Update 1.**
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. V **cesta k souboru aktualizace**, zadejte název souboru pro aktualizaci nebo opravu hotfix. Můžete také procházet k instalačnímu souboru aktualizaci nebo opravu hotfix Pokud umístěn ve sdílené síťové složce. Klikněte na tlačítko **Použít**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Zobrazí se upozornění. Zadané virtuální pole je jeden uzel zařízení, po tuto aktualizaci nenainstalujete, restartování zařízení a dojde k výpadku. Klikněte na ikonu zaškrtnutí.
   
   ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Aktualizace spustí. Po úspěšné aktualizaci zařízení restartuje. Místní uživatelské rozhraní není dostupný v této hodnotě duration.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Po dokončení restartování se otevře **přihlášení** stránky. Chcete-li ověřit, že zařízení software byl aktualizován, v místní webového uživatelského rozhraní, přejděte na **údržby** > **aktualizace softwaru**. Zobrazit software verze musí být **10.0.0.0.0.10296** pro Update 1.0.
   
   > [!NOTE]
   > Verze softwaru jsme sestavu mírně odlišné způsobem místního webového uživatelského rozhraní a portálu Azure. Například místní webového uživatelského rozhraní sestavy **10.0.0.0.0.10296** a Azure portálu sestavy **10.0.10296.0** pro stejnou verzi.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Opakujte kroky 2 až 4 nainstalujte opravu zabezpečení systému Windows pomocí souboru `windows8.1-kb4012213-x64`. Po instalaci restartuje virtuální pole a je třeba se přihlásit do místního webového uživatelského rozhraní.

> [!NOTE]
> Pokud použité Update 1 se přímo do zařízení s verzí před aktualizací 0,6, chybí některé aktualizace. Microsoft Support Požádejte prosím o dalších krocích.

## <a name="next-steps"></a>Další kroky

Další informace o [Správa pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md).
