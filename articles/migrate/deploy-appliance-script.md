---
title: Nastavení zařízení Azure Migrate pomocí skriptu
description: Přečtěte si, jak nastavit zařízení Azure Migrate pomocí skriptu
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337685"
---
# <a name="set-up-an-appliance-with-a-script"></a>Nastavení zařízení se skriptem

Tento článek popisuje, jak nastavit [zařízení Azure Migrate pomocí](deploy-appliance.md) instalačního skriptu Prostředí PowerShell.

Skript poskytuje:
- Alternativa k nastavení zařízení pomocí šablony OVA pro posouzení a migraci virtuálních zařízení VMware bez agentů.
- Alternativa k nastavení zařízení pomocí šablony virtuálního pevného disku pro posouzení a migraci virtuálních zařízení Hyper-V.
- Pro posouzení fyzických serverů (nebo virtuálních společností, které chcete migrovat jako fyzické servery), je skript jedinou metodou pro nastavení zařízení.

## <a name="prerequisites"></a>Požadavky

Skript nastaví zařízení Azure Migrate na existujícím fyzickém počítači nebo virtuálním počítači.

- Počítač, který bude fungovat jako zařízení, musí být se systémem Windows Server 2016 s 32 GB paměti, osmi virtuálními procesory, přibližně 80 GB diskového úložiště a externím virtuálním přepínačem. Vyžaduje statickou nebo dynamickou IP adresu a přístup k internetu.
- Před nasazením zařízení zkontrolujte podrobné požadavky zařízení pro [virtuální zařízení VMware](migrate-appliance.md#appliance---vmware), [virtuální chody Hyper-V](migrate-appliance.md#appliance---hyper-v)a [fyzické servery](migrate-appliance.md#appliance---physical).
- Nespouštějte skript na existující zařízení Azure Migrate.


## <a name="download-the-script"></a>Stáhněte si skript

1. Vyhledejte počítač nebo virtuální počítač, který bude fungovat jako zařízení Azure Migrate.
2. Na stroji postupujte takto:

    - Chcete-li zařízení používat s virtuálními zařízeními VMware nebo virtuálními zařízeními Hyper-V, [stáhněte si](https://go.microsoft.com/fwlink/?linkid=2105112) složku se zipem obsahující instalační skript a msis.
    - Chcete-li zařízení používat s fyzickými servery, stáhněte si skript z portálu Migrace Azure, jak je popsáno v tomto [kurzu](tutorial-assess-physical.md#set-up-the-appliance).

## <a name="verify-file-security"></a>Ověření zabezpečení souborů

Před nasazením zkontrolujte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spuštěním následujícího příkazu vygenerujte soubor hash pro zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Ověřte, zda generované hodnoty hash odpovídají těmto nastavením (pro nejnovější verzi zařízení):

    **Algoritmus** | **Hodnota hash**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cdd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e6de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>Spuštění skriptu

Zde je to, co skript dělá:

- Nainstaluje agenty a webovou aplikaci.
- Nainstaluje role systému Windows, včetně služby Aktivace systému Windows, služby IIS a prostředí PowerShell ISE.
- Stáhne a nainstaluje přepnutelný modul služby IIS. [Další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) s trvalým nastavením pro Migraci Azure.
- Vytvoří soubory protokolu a konfigurační soubory následujícím způsobem:
    - **Soubory konfigurace**: %ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**: %ProgramData%\Microsoft Azure\Protokoly

Spuštění skriptu:

1. Extrahujte soubor zip do složky v počítači, která bude hostitelem zařízení.
2. Spusťte powershell v počítači s oprávněními správce (se zvýšenými oprávněními).
3. Změňte adresář prostředí PowerShell na složku obsahující obsah extrahovaný ze staženého zipového souboru.
4. Spusťte skript **AzureMigrateInstaller.ps1** takto:

    - Pro společnost VMware: 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - Pro Hyper-V:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - Pro fyzické servery:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. Po úspěšném spuštění skriptu spustí webové aplikace zařízení, takže můžete nastavit zařízení. Pokud narazíte na nějaké problémy, můžete zobrazit protokoly skriptů na adrese C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

## <a name="next-steps"></a>Další kroky

Po nastavení zařízení pomocí skriptu postupujte podle následujících pokynů:

- Nastavte přístroj pro [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Nastavte přístroj pro [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Nastavte zařízení pro [fyzické servery](how-to-set-up-appliance-physical.md).