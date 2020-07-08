---
title: Nastavení testovacího prostředí pro učení vývoje mobilních aplikací pomocí Android Studio
titleSuffix: Azure Lab Services
description: Naučte se, jak nastavit testovací prostředí pro výuku třídy pro vývoj dat mobilní aplikace, která používá Android Studio.  Článek také popisuje úpravy, které je třeba provést při použití Android Studio na virtuálním počítači v Azure.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: ff996a096cddbb85f1e1c84cd051c18a7ab4ad79
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444977"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Nastavení testovacího prostředí pro učení dat vývoje mobilních aplikací pomocí Android Studio

Tento článek vám ukáže, jak nastavit úvodní třídu pro vývoj mobilních aplikací.  Tato třída se zaměřuje na mobilní aplikace pro Android, které mohou být publikovány na [obchod Google Play](https://play.google.com/store/apps).  Studenti se dozvíte, jak používat [Android Studio](https://developer.android.com/studio) k sestavování aplikací.  [Emulátor sady Visual Studio pro Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) slouží k místnímu testování aplikace.

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí

K nastavení tohoto testovacího prostředí potřebujete předplatné Azure a účet testovacího prostředí, abyste mohli začít. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/), ještě než začnete. Po získání předplatného Azure můžete vytvořit nový účet testovacího prostředí v Azure Lab Services. Další informace o vytvoření nového účtu testovacího prostředí najdete v [kurzu nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).  Můžete použít i existující účet testovacího prostředí.

Postupujte podle [kurzu nastavení laboratorního prostředí](tutorial-setup-classroom-lab.md) pro vytvoření nového testovacího prostředí a pak použijte následující nastavení:

| Velikost virtuálního počítače | Image |
| -------------------- | ----- |
| Střední (vnořená virtualizace) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Konfigurace počítače šablony

Až se šablona vytvoří, [Spusťte počítač a připojte se k němu](how-to-create-manage-template.md#update-a-template-vm) , abyste mohli dokončit následující úlohy:

1. Přidat roli technologie Hyper-V
2. Stáhněte a nainstalujte Java.  
3. Stáhněte a nainstalujte emulátor sady Visual Studio pro Android.
4. Stáhněte a nainstalujte Android Studio.
5. Nakonfigurujte emulátor sady Visual Studio pro Android Studio.

## <a name="add-hyper-v-role"></a>Přidat roli technologie Hyper-V

Technologie Hyper-V musí být povolená pro úspěšnou instalaci emulátoru sady Visual Studio pro Android.  Postupujte podle pokynů v článku [Jak povolit vnořenou virtualizaci v článku o šabloně virtuálního počítače](how-to-enable-nested-virtualization-template-vm.md) .

## <a name="install-java"></a>Nainstalovat Java

Android Studio vyžaduje Java.  Použijte následující postup ke stažení nejnovější verze Java.

1. Přejděte na [stránku pro stažení Java](https://www.java.com/download/). Klikněte na tlačítko pro **stažení Java** .
2. Na webové stránce 64 Windows for Java klikněte na tlačítko s popiskem **Souhlasím a začněte stahovat zdarma**.
3. Až se zobrazí instalační program **Java** , klikněte na **nainstalovat**.
4. Počkejte, až se název instalačního programu změní na **Nastavení Java – dokončeno**.  Klikněte na tlačítko **Zavřít** .

## <a name="install-visual-studio-emulator-for-android"></a>Nainstalovat emulátor sady Visual Studio pro Android

K místnímu otestování aplikace pro Android se musí použít virtualizované verze zařízení s Androidem.  K dispozici je několik emulátorů Androidu, které vývojářům umožní otestovat svoji aplikaci ze svého počítače.  Používáme emulátor sady Visual Studio pro Android, protože se jedná o emulátor, který podporuje vnořenou virtualizaci.  Vzhledem k tomu, že virtuální počítač služby testovacího prostředí už je virtuálním počítačem, potřebujeme emulátor, který podporuje vnořenou virtualizaci.  Integrovaný emulátor pro Android Studio nepodporuje vnořenou virtualizaci.  Pokud chcete zjistit, které emulátory podporují vnořenou virtualizaci, přečtěte si téma [hardwarová akcelerace pro výkon emulátoru (Hyper-V & modul HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

K stažení a instalaci emulátoru sady Visual Studio pro Android použijte následující pokyny.

1. Přejděte na domovskou stránku [emulátoru sady Visual Studio pro Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) .
2. Klikněte na tlačítko **stáhnout emulátor** .
3. Po stažení vs_emulatorsetup.exe spusťte spustitelný soubor.
4. Jakmile se zobrazí dialogové okno instalace sady Visual Studio, klikněte na tlačítko **nainstalovat** .
5. Počkejte, až se instalační program dokončí.  Klikněte na tlačítko **restartovat nyní** a restartujte počítač a dokončete instalaci.

Před nasazením aplikace pomocí Android Studio nejprve spusťte emulátor.  Další informace o emulátoru sady Visual Studio pro Android najdete v [dokumentaci k emulátoru sady Visual Studio pro Android](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android).

## <a name="install-android-studio"></a>Nainstalovat Android Studio

Pro stažení a instalaci [Android Studio](https://developer.android.com/studio)postupujte podle následujících pokynů.

1. Přejděte na [stránku pro stažení Android Studio](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Internet Explorer není tímto webem podporován.
2. Klikněte na spustitelný balíček Windows (64-bit) Android Studio.
3. Přečtěte si právní doložky napsané v místním okně.  Až budete připraveni pokračovat, zaškrtněte políčko **přečetl jsem si a souhlasím s výše uvedenými podmínkami a ujednáními** a klikněte na tlačítko **Stáhnout Android Studio pro Windows** .
4. Po stažení instalačního souboru instalačního programu Android Studio spusťte spustitelný soubor.
5. Na stránce **vítá Android Studio vás instalační program** **Instalace Android Studio** klikněte na tlačítko **Další**.
6. Na stránce **nastavení konfigurace** klikněte na **Další**.
7. Na stránce **Zvolit složku nabídky Start** klikněte na **nainstalovat**.
8. Počkejte, až se instalace dokončí.
9. Na stránce **Instalace byla dokončena** klikněte na tlačítko **Další**.
10. Na stránce **dokončení instalace Android Studio** .  Klikněte na **Finish** (Dokončit).
11. Po dokončení instalace se Android Studio automaticky spustí.
12. V dialogovém okně **importovat nastavení Androidu z...** vyberte **Neimportovat nastavení**. Klikněte na **OK**.
13. Na **úvodní** stránce **Průvodce instalací Android Studio**klikněte na **Další**.
14. Na stránce **typ instalace** vyberte možnost **standardní**. Klikněte na **Další**.
15. Na stránce **vybrat motiv uživatelského rozhraní** vyberte požadovaný motiv. Klikněte na **Další**.
16. Na stránce **ověřit nastavení** klikněte na **Další**.
17. Na stránce **stahování součástí** počkejte, dokud nebudou staženy všechny součásti.  Klikněte na **Finish** (Dokončit).

    > [!IMPORTANT]
    > Očekává se, že instalace modul HAXM selhala.  MODUL HAXM nepodporuje vnořenou virtualizaci. to je důvod, proč jsme nainstalovali emulátor sady Visual Studio pro Android výše v tomto článku.

18. Po dokončení Průvodce instalací se zobrazí dialogové okno **Vítá vás Android Studio** .

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Konfigurace Android Studio a emulátoru sady Visual Studio pro Android

Android Studio je skoro připravený k použití.  Pořád musíme říct emulátor sady Visual Studio pro Android, kde je nainstalovaná Android SDK.  Všechny emulátory spuštěné v aplikaci Visual Studio pro Android se zobrazí jako cíle nasazení pro Android Studio ladění.

Musíme nastavit konkrétní klíč registru, který bude informovat emulátor sady Visual Studio pro Android, kde se nachází sada Android SDK.  Chcete-li nastavit potřebný klíč registru, spusťte následující skript.  Níže uvedený skript prostředí PowerShell předpokládá výchozí umístění instalace pro sadu Android SDK.  Pokud jste sadu Android SDK nainstalovali v jiném umístění, `$androidSdkPath` před spuštěním skriptu upravte hodnotu.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Restartujte emulátor sady Visual Studio pro Android a Android Studio tak, aby se nové nastavení používalo.

Spusťte verzi, kterou potřebujete v emulátoru sady Visual Studio.  Zobrazí se jako cíl nasazení aplikace pro Android v Android studiu.  Minimální verze Android Studioho projektu musí podporovat verzi spuštěnou v emulátoru sady Visual Studio pro Android.  Nyní jste připraveni vytvořit a ladit projekty pomocí Android Studio a emulátoru sady Visual Studio pro Android.  Pokud máte nějaké problémy, přečtěte si téma Poradce při potížích s emulátorem Android.

## <a name="cost"></a>Náklady

Pokud byste chtěli odhadnout náklady na toto testovací prostředí, můžete postupovat podle níže uvedeného příkladu.
Pro třídu 25 studentů s 20 hodinami plánovaného času třídy a 10 hodin pro domácí úlohy nebo přiřazení by se cena za testovací prostředí měla  

25 studentů \* (20 naplánovaných + 10 kvót) hodin × 55 jednotek testovacího prostředí × 0,01 USD za hodinu = 412,5 USD

Další podrobnosti o cenách najdete v článku [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí.

- [Vytvoření a Správa šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users)
