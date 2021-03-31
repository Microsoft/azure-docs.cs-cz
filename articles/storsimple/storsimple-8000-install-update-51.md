---
title: Instalace aktualizace 5,1 na zařízení s StorSimple 8000 series | Microsoft Docs
description: Vysvětluje, jak nainstalovat aktualizaci StorSimple 8000 Series Update 5,1 na zařízení řady StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: alkohli
ms.openlocfilehash: 5b9958f3dd497aa612a92947b8d968439ef9d0e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91575958"
---
# <a name="install-update-51-on-your-storsimple-device"></a>Instalace aktualizace 5,1 na zařízení StorSimple

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak nainstalovat Update 5,1 na zařízení StorSimple s dřívější verzí softwaru prostřednictvím Azure Portal. <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

Aktualizace 5,1 obsahuje aktualizace zabezpečení bez přerušení. Nerušivé nebo pravidelné aktualizace lze použít prostřednictvím Azure Portal <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * Aktualizace 5,1 je povinná aktualizace a měla by být nainstalovaná hned. Další informace najdete v [poznámkách k verzi Update 5,1](storsimple-update51-release-notes.md).
> * Sada ručních a automatických předběžných kontrol se provádí před instalací k určení stavu zařízení z hlediska stavu hardwaru a připojení k síti. Tyto předběžné kontroly se provádějí jenom v případě, že se aktualizace použijí z Azure Portal.
> * Pokud chcete nainstalovat pomocí metody hotfix, kontaktujte [Podpora Microsoftu](mailto:support@microsoft.com).

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Nainstalujte aktualizaci 5,1 prostřednictvím Azure Portal

Provedením následujících kroků aktualizujte zařízení tak, aby se [aktualizovalo 5,1](storsimple-update51-release-notes.md).

> [!NOTE]
> Microsoft z tohoto zařízení vyžádá další diagnostické informace. V důsledku toho je v případě, že náš provozní tým identifikuje zařízení, u kterých dochází k problémům, jsme lépe zakázali shromažďovat informace ze zařízení a diagnostikovat problémy.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Instalace aktualizace z webu Azure Portal

1. Na stránce služby StorSimple vyberte zařízení.

    ![Vybrat zařízení](./media/storsimple-8000-install-update-51/update1.png)

2. Přejděte na **nastavení zařízení**  >  **aktualizace zařízení**.

    ![Snímek obrazovky okna nastavení s možností aktualizace zařízení s názvem.](./media/storsimple-8000-install-update-51/update2.png)

3. Pokud jsou k dispozici nové aktualizace, zobrazí se oznámení. Případně můžete v okně **aktualizace zařízení** kliknout na **Vyhledat aktualizace**. Vytvoří se úloha vyhledávání dostupných aktualizací. Po úspěšném dokončení úlohy se zobrazí zpráva.

    ![Snímek obrazovky s oknem nastavení s možností aktualizace zařízení s názvem a v okně aktualizace zařízení s novými pravidelnými aktualizacemi je k dispozici zpráva s názvem.](./media/storsimple-8000-install-update-51/update3.png)

4. Doporučujeme, abyste si před instalací aktualizace na zařízení prošli poznámky k verzi. Chcete-li použít aktualizace, klikněte na tlačítko **instalovat aktualizace**. V okně **Potvrdit běžné aktualizace** si přečtěte předpoklady, které je potřeba provést před instalací aktualizací. Zaškrtnutím políčka označíte, že jste připraveni aktualizovat zařízení, a pak klikněte na **nainstalovat**.

    ![Snímek obrazovky s oknem aktualizace zařízení s nainstalovanou možností instalovat aktualizace a potvrdit pravidelné aktualizace s možností souhlasu a instalace s názvem.](./media/storsimple-8000-install-update-51/update4.png)

5. Spustí se sada kontrol požadavků. Mezi tyto kontroly patří:
   
   * **Kontroly stavu kontrolerů** pro ověření, že oba kontrolery zařízení jsou v pořádku a online.
   * **Kontroly stavu hardwarových komponent** pro ověření, že všechny hardwarové komponenty v zařízení StorSimple jsou v pořádku.
   * **Kontroly rozhraní DATA 0** pro ověření, že je na zařízení povolené rozhraní DATA 0. Pokud toto rozhraní není povolené, musíte jej povolit a poté to zkusit znovu.

     Aktualizace se stáhne a nainstaluje jenom v případě, že se všechny kontroly úspěšně dokončily. Na probíhání kontrol budete upozorněni. Pokud předkontroly selžou, budete mít k dispozici důvody pro selhání. Vyřešte tyto problémy a potom operaci opakujte. Pokud si s těmito problémy neporadíte sami, bude muset kontaktovat podporu Microsoftu.

7. Po úspěšném dokončení předkontrol se vytvoří úloha aktualizace. Po úspěšném vytvoření úlohy aktualizace se zobrazí upozornění.
   
    ![Snímek obrazovky s oznámením, které říká "spouštění úlohy aktualizací softwaru"](./media/storsimple-8000-install-update-51/update6.png)
   
    Aktualizace se potom nainstaluje na vašem zařízení.

9. Dokončení aktualizace trvá několik hodin. Podrobnosti o úloze můžete kdykoli zobrazit výběrem úlohy aktualizace a kliknutím na **Podrobnosti**.

    ![Snímek obrazovky okna aktualizace zařízení s možností stáhnout a nainstalovat probíhající aktualizace softwaru, která se nazývá a nainstaluje okno instalovat aktualizace.](./media/storsimple-8000-install-update-51/update8.png)

     Můžete také monitorovat průběh úlohy aktualizace z **nastavení zařízení > úloh**. V okně **úlohy** vidíte průběh aktualizace.

     ![Snímek obrazovky okna nastavení s úlohami, které se vyvolaly, a okno úlohy znázorňující průběh aktualizace.](./media/storsimple-8000-install-update-51/update7.png)

10. Po dokončení úlohy přejděte do **nastavení zařízení > aktualizace zařízení**. Verze softwaru by se teď měla aktualizovat.


Ověřte, že na zařízení běží **StorSimple 8000 Series Update 5,1 (6.3.9600.17885)**. **Datum poslední aktualizace** by mělo být změněno.
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [vydání aktualizace 5,1](storsimple-update51-release-notes.md).
