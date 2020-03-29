---
title: Nastavení testovacího prostředí pro výuku vývoje mobilních aplikací pomocí Android Studia
titleSuffix: Azure Lab Services
description: Zjistěte, jak nastavit testovací prostředí pro výuku třídy vývoje mobilních aplikací pro datové aplikace, která používá Android Studio.  Článek bude také diskutovat o úpravách, které je třeba provést při použití Android Studio na virtuálním počítači v Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849783"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Nastavení testovacího prostředí pro výuku vývoje datových mobilních aplikací pomocí Android Studia

Tento článek vám ukáže, jak nastavit úvodní třídu vývoje mobilních aplikací.  Tato třída se zaměřuje na mobilní aplikace pro Android, které mohou být publikovány v [Obchodě Google Play](https://play.google.com/store/apps).  Studenti se učí, jak používat [Android Studio](https://developer.android.com/studio) k vytváření aplikací.  [Visual Studio Emulátor pro Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) se používá k testování aplikace místně.

## <a name="lab-configuration"></a>Konfigurace laboratoře

Chcete-li nastavit toto testovací prostředí, budete potřebovat předplatné Azure a účet testovacího prostředí, abyste mohli začít. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete. Jakmile získáte předplatné Azure, můžete si vytvořit nový účet testovacího prostředí ve službách Azure Lab Services. Další informace o vytvoření nového účtu testovacího prostředí naleznete [v kurzu k nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).  Můžete také použít existující účet testovacího prostředí.

Podle [kurzu k nastavení testovacího prostředí ve třídě](tutorial-setup-classroom-lab.md) vytvořte nové testovací prostředí a pak použijte následující nastavení:

| Velikost virtuálního počítače | Image |
| -------------------- | ----- |
| Střední (vnořená virtualizace) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Konfigurace počítače šablony

Po dokončení vytváření šablony počítače [spusťte počítač a připojte se k němu](how-to-create-manage-template.md#update-a-template-vm) a dokončete následující úkoly:

1. Přidat roli Hyper-V
2. Stáhněte a nainstalujte Jazyk Java.  
3. Stáhněte a nainstalujte emulátor Visual Studia pro Android.
4. Stáhněte a nainstalujte Android Studio.
5. Konfigurace emulátoru Visual Studio pro Android Studio.

## <a name="add-hyper-v-role"></a>Přidat roli Hyper-V

Hyper-V musí být povolena pro úspěšnou instalaci emulátoru Visual Studio pro Android.  Postupujte podle pokynů v [tom, jak povolit vnořenou virtualizaci v článku virtuálního počítače šablony.](how-to-enable-nested-virtualization-template-vm.md)

## <a name="install-java"></a>Instalace Javy

Android Studio vyžaduje Java.  Podle následujících kroků stáhněte nejnovější verzi Javy.

1. Přejděte na [stránku pro stažení v jazyce Java](https://www.java.com/download/). Klikněte na tlačítko **Stažení v Jazyce Java.**
2. Na 64bitové webové stránce Windows for Java klikněte na tlačítko s označením **Souhlasím a Spustit bezplatné stažení**.
3. Po zobrazení instalačního programu instalačního programu instalačního programu **v jazyce Java** klepněte na tlačítko **Nainstalovat**.
4. Počkejte, až se název instalačního programu změní na **nastavení v jazyce Java – dokončení**.  Klikněte na **tlačítko Zavřít.**

## <a name="install-visual-studio-emulator-for-android"></a>Instalace emulátoru Visual Studia pro Android

Chcete-li testovat aplikaci pro Android místně, musí používat virtualizovanou verzi zařízení android.  Existuje několik Android emulátory k dispozici, které umožní vývojáři otestovat jejich aplikace z jejich počítače.  Používáme Emulátor Visual Studio pro Android, protože se jedná o emulátor, který podporuje vnořenou virtualizaci.  Vzhledem k tomu, že virtuální počítač služby Lab je již virtuální počítač, potřebujeme emulátor, který podporuje vnořené virtualizace.  Vestavěný emulátor pro Android Studio nepodporuje vnořené virtualizace.  Chcete-li zjistit, které emulátory podporují vnořenou virtualizaci, naleznete [v tématu hardwarová akcelerace pro výkon emulátoru (Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

Pomocí následujících pokynů stáhněte a nainstalujte emulátor Visual Studia pro Android.

1. Přejděte na [emulátor visual studia pro](https://visualstudio.microsoft.com/vs/msft-android-emulator/) domovskou stránku Androidu.
2. Klikněte na tlačítko **Stáhnout emulátor.**
3. Po stažení vs_emulatorsetup.exe spusťte spustitelný soubor.
4. Po zobrazení dialogového okna nastavení sady Visual Studio klepněte na tlačítko **Instalovat.**
5. Počkejte na dokončení instalačního programu.  Klepnutím na tlačítko **Restartovat nyní** restartujte počítač a dokončete instalaci.

Před nasazením aplikace pomocí Android Studia spusťte emulátor.  Další informace o emulátoru Visual Studio pro Android naleznete v [tématu Visual Studio Emulator for Android documentation](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android).

## <a name="install-android-studio"></a>Instalace aplikace Android Studio

Chcete-li stáhnout a nainstalovat [aplikaci Android Studio,](https://developer.android.com/studio)postupujte podle následujících pokynů.

1. Přejděte na [stránku pro stažení aplikace Android Studio](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Aplikace Internet Explorer není tímto webem podporována.
2. Klikněte na balíček Windows (64-bit) spustitelný Android Studio.
3. Přečtěte si právní termíny napsané v pop-up.  Až budete připraveni pokračovat, zkontrolujte, zda **jsem si přečetl a souhlasím s výše uvedenými podmínkami** a klikněte na tlačítko Stáhnout Android Studio pro **Windows.**
4. Po stažení spustitelného souboru nastavení Android Studio spusťte spustitelný soubor.
5. Na stránce **Welcome to Android Studio Setup** page **instalátoru nastavení androida Studia** klepněte na tlačítko **Další**.
6. Na stránce **Nastavení konfigurace** klepněte na tlačítko **Další**.
7. Na stránce **Zvolit složku nabídky Start** klepněte na tlačítko **Instalovat**.
8. Počkejte na dokončení instalace.
9. Na stránce **Dokončení instalace** klepněte na tlačítko **Další**.
10. Na stránce **Dokončení nastavení studia Android.**  Klikněte na **Finish** (Dokončit).
11. Android Studio se automaticky spustí po dokončení instalace.
12. V dialogovém okně **Importovat nastavení Androidu z...** vyberte **Možnost Neimportovat nastavení**. Klikněte na tlačítko **OK**.
13. Na **úvodní** stránce **Průvodce instalací studia Android**klepněte na tlačítko **Další**.
14. Na stránce **Typ instalace** zvolte **Standardní**. Klikněte na **Další**.
15. Na stránce **Vybrat motiv ui** vyberte požadovaný motiv. Klikněte na **Další**.
16. Na stránce **Ověřit nastavení** klikněte na **další**.
17. Na stránce **Stahování součástí** počkejte, dokud nebudou staženy všechny součásti.  Klikněte na **Finish** (Dokončit).

    > [!IMPORTANT]
    > Očekává se, že instalace HAXM se nezdaří.  HAXM nepodporuje vnořené virtualizace, což je důvod, proč jsme nainstalovali Visual Studio Emulátor pro Android dříve v tomto článku.

18. Po dokončení průvodce instalací se zobrazí dialogové okno **Vítá vás Studio pro Android.**

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Konfigurace androidového studia a emulátoru sady Visual Studio pro Android

Android Studio je téměř připraven k použití.  Stále potřebujeme říct Emulátor u sady Visual Studio pro Android, kde je nainstalována sada Android SDK.  To způsobí, že všechny emulátory spuštěné v sadě Visual Studio pro Android zobrazit jako cíle nasazení pro ladění Android Studio.

Musíme nastavit konkrétní klíč registru sdělit Visual Studio emulátor pro Android, kde je umístěn Android Sdk.  Chcete-li nastavit potřebný klíč registru, spusťte níže uvedený skript.  Níže uvedený skript prostředí PowerShell předpokládá výchozí umístění instalace sady Android Sdk.  Pokud jste aplikaci Android Sdk nainstalovali `$androidSdkPath` do jiného umístění, upravte hodnotu pro před spuštěním skriptu.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Restartujte emulátor sady Visual Studio pro Android a Android Studio, aby se používalo nové nastavení.

Spusťte verzi, kterou potřebujete v emulátoru Visual Studio.  Zobrazí se jako cíl nasazení pro vaši aplikaci pro Android ve studiu pro Android.  Minimální verze pro projekt Android Studio musí podporovat verzi spuštěnou v emulátoru Visual Studio pro Android.  Nyní jste připraveni vytvářet a ladit projekty pomocí Android Studio a Visual Studio Emulátor pro Android.  Pokud máte nějaké problémy, přečtěte si článek Řešení potíží s emulátorem Androidu.

## <a name="cost"></a>Náklady

Pokud chcete odhadnout náklady na tuto testovací prostředí, můžete postupovat podle následujícího příkladu.
Pro třídu 25 studentů s 20 hodinami plánovaného času ve třídě a 10 hodinami kvóty pro domácí úkoly nebo úkoly by cena za laboratoř byla  

25 \* studentů (20 plánovaných + 10 kvót) hodin * 55 laboratorních jednotek * 0,01 USD za hodinu = 412.5 USD

Další podrobnosti o cenách najdete v tématu [Ceny služeb Azure Lab](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Další kroky

Další kroky jsou společné pro nastavení libovolného testovacího prostředí.

- [Vytvoření a správa šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavení plánu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mailové registrační odkazy pro studenty](how-to-configure-student-usage.md#send-invitations-to-users)
