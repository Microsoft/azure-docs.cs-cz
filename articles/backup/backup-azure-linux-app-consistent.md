---
title: Zálohy virtuálních počítačů s Linuxem konzistentní s aplikacemi
description: Vytvářejte zálohy virtuálních počítačů s Linuxem konzistentní s aplikacemi do Azure. Tento článek vysvětluje konfiguraci rozhraní skriptů pro zálohování virtuálních počítačů s Linuxem nasazenými v Azure. Tento článek obsahuje také informace o řešení potíží.
ms.reviewer: anuragm
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 36eeb9f63c67a01bf37412101e23be035596de94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173012"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Zálohování virtuálních počítačů Azure Linux konzistentní s aplikacemi

Při pořizování snímků záloh virtuálních počítačů konzistence aplikace znamená, že vaše aplikace se spustí při spuštění virtuálních počítačích po obnovení. Jak si dokážete představit, konzistence aplikace je nesmírně důležité. Chcete-li zajistit, aby vaše virtuální počítače s Linuxem byly konzistentní s aplikací, můžete použít linuxový rámec před skriptem a postskript, který umožňuje zálohovat konzistentní aplikace. Rozhraní před skripty a postskriptu podporuje virtuální počítače Linuxu nasazené v Azure Resource Manageru. Skripty pro konzistenci aplikací nepodporují virtuální počítače nasazené Správcem služeb nebo virtuální počítače s Windows.

## <a name="how-the-framework-works"></a>Jak rámec funguje

Rámec poskytuje možnost spouštět vlastní předběžné skripty a post-skripty při pořizování snímků virtuálních počítače. Předběžné skripty se spustí těsně před pořízením snímku virtuálního počítače a postskripty se spustí ihned po pořízení snímku virtuálního počítače. Předskripty a post-skripty poskytují flexibilitu při řízení vaší aplikace a prostředí, zatímco pořizovat snímky virtuálních aplikací.

Předběžné skripty vyvolávají nativní aplikační api, která uvolna vi a vyprázdnění obsahu v paměti na disk. Tyto akce zajistí, že snímek je konzistentní v aplikaci. Post-scripts použít nativní aplikace API rozmrazit VOs, které umožňují aplikaci obnovit normální operace po snímku virtuálního počítače.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Postup konfigurace předskriptu a postskriptu

1. Přihlaste se jako root uživatel k virtuálnímu počítači SIP, který chcete zálohovat.

2. Z [GitHubu](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)stáhněte **VMSnapshotScriptPluginConfig.json** a zkopírujte ho do složky **/etc/azure** pro všechny virtuální počítače, které chcete zálohovat. Pokud složka **/etc/azure** neexistuje, vytvořte ji.

3. Zkopírujte předskript a post-script pro vaši aplikaci na všech virtuálních počítačích, které chcete zálohovat. Skripty můžete zkopírovat do libovolného umístění na virtuálním počítači. Nezapomeňte aktualizovat úplnou cestu k souborům skriptu v souboru **VMSnapshotScriptPluginConfig.json.**

4. Zajistěte následující oprávnění pro tyto soubory:

   - **VMSnapshotScriptPluginConfig.json**: Oprávnění "600." Například pouze "root" uživatel by měl mít oprávnění "číst" a "psát" k tomuto souboru a žádný uživatel by neměl mít oprávnění "spustit".

   - **Soubor před skriptem**: Oprávnění "700".  Například pouze "root" uživatel by měl mít oprávnění "číst", "psát" a "spustit" k tomuto souboru.

   - **Post-skript** Povolení "700." Například pouze "root" uživatel by měl mít oprávnění "číst", "psát" a "spustit" k tomuto souboru.

   > [!IMPORTANT]
   > Rámec dává uživatelům hodně energie. Zabezpečte rámec a zajistěte, aby pouze "root" uživatel měl přístup ke kritickým souborům JSON a skriptů.
   > Pokud nejsou splněny požadavky, skript se nespustí, což má za následek selhání systému souborů a nekonzistentní zálohování.
   >

5. Konfigurace **souboru VMSnapshotScriptPluginConfig.json,** jak je popsáno zde:
    - **pluginName**: Nechte toto pole tak, jak je, nebo vaše skripty nemusí fungovat podle očekávání.

    - **preScriptLocation**: Zadejte úplnou cestu k předběžnému skriptu na virtuálním počítači, který bude zálohován.

    - **postScriptLocation**: Zadejte úplnou cestu post-script na virtuálním počítači, který bude zálohován.

    - **preScriptParams**: Zadejte volitelné parametry, které je třeba předat předskriptu. Všechny parametry by měly být v uvozovkách. Pokud používáte více parametrů, oddělte parametry čárkou.

    - **postScriptParams**: Zadejte volitelné parametry, které je třeba předat post-script. Všechny parametry by měly být v uvozovkách. Pokud používáte více parametrů, oddělte parametry čárkou.

    - **preScriptNoOfRetries**: Nastavte, kolikrát má být předskript opakován, pokud dojde k chybě před ukončením. Nula znamená pouze jeden pokus a žádné opakování, pokud dojde k selhání.

    - **postScriptNoOfRetries**: Nastavte, kolikrát má být post-script opakován, pokud dojde k nějaké chybě před ukončením. Nula znamená pouze jeden pokus a žádné opakování, pokud dojde k selhání.

    - **timeoutInSeconds**: Zadejte jednotlivé časové limity pro předběžný a post-script (maximální hodnota může být 1800).

    - **continueBackupOnFailure**: Nastavte tuto hodnotu na **hodnotu true,** pokud chcete, aby služba Azure Backup přecvála zpět na konzistentní zálohu systému souborů a selhání při selhání předskriptu nebo po skriptu. Nastavení na **false** selže zálohování v případě selhání skriptu (s výjimkou případů, kdy máte jednodiskový virtuální počítače, který spadá zpět do zálohy konzistentní s havárií bez ohledu na toto nastavení).

    - **fsFreezeEnabled**: Určete, zda linuxové fsfreeze by měla být volána při pořizování snímku virtuálního počítače k zajištění konzistence systému souborů. Doporučujeme zachovat toto nastavení nastaveno na **hodnotu true,** pokud vaše aplikace nemá závislost na zakázání fsfreeze.

    - **ScriptsExecutionPollTimeSeconds**: Nastavte čas, po který má rozšíření spát mezi jednotlivými polly, aby bylo spuštění skriptu. Například pokud je hodnota 2, rozšíření zkontroluje, zda před a po spuštění skriptu dokončena každé 2 sekundy. Minimální a maximální hodnota, kterou může trvat, je 1 a 5. Hodnota by měla být přísně celé číslo.

6. Rozhraní skriptu je nyní nakonfigurováno. Pokud je záloha virtuálního počítače už nakonfigurovaná, další záloha vyvolá skripty a spustí zálohování konzistentní s aplikací. Pokud záloha virtuálních počítačů není nakonfigurovaná, nakonfigurujte ji pomocí [zálohování virtuálních počítačů Azure do trezorů služby Recovery Services.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Řešení potíží

Ujistěte se, že přidáte příslušné protokolování při psaní předskriptu a post-script, a zkontrolujte protokoly skriptů opravit všechny problémy se skripty. Pokud máte stále potíže se spouštěním skriptů, další informace naleznete v následující tabulce.

| Chyba | Chybová zpráva | Doporučená akce |
| ------------------------ | -------------- | ------------------ |
| Předspuštění skriptu se nezdařilo. |Předskript vrátil chybu, takže zálohování nemusí být konzistentní s aplikací.| Podívejte se na protokoly selhání pro váš skript opravit problém.|  
|Spuštění po spuštění skriptu se nezdařilo. |Post-script vrátil chybu, která by mohla ovlivnit stav aplikace. |Podívejte se na protokoly selhání pro váš skript opravit problém a zkontrolujte stav aplikace. |
| Pre-ScriptnotFound |Předběžný skript nebyl nalezen v umístění, které je určeno v konfiguračním souboru **VMSnapshotScriptPluginConfig.json.** |Ujistěte se, že předskript je přítomen na cestě, která je zadána v konfiguračním souboru, abyste zajistili zálohování konzistentní s aplikací.|
| Post-ScriptNotFound |Post-script nebyl nalezen v umístění, které je zadáno v konfiguračním souboru **VMSnapshotScriptPluginConfig.json.** |Ujistěte se, že post-script je přítomen na cestě, která je zadána v konfiguračním souboru k zajištění zálohování konzistentní aplikace.|
| Soubor IncorrectPluginhostFile |Soubor **Pluginhost,** který je dodáván s příponou VmSnapshotLinux, je poškozen, takže předskript a post-script nelze spustit a záloha nebude konzistentní s aplikací.| Odinstalujte rozšíření **VmSnapshotLinux** a bude automaticky přeinstalováno s další zálohou, abyste problém vyřešili. |
| Nesprávný soubor JSONConfigFile | Soubor **VMSnapshotScriptPluginConfig.json** je nesprávný, takže předskript a post-script nelze spustit a záloha nebude konzistentní s aplikací. | Stáhněte si kopii z [GitHubu](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) a znovu ji nakonfigurujte. |
| InsufficientPermissionforPre-Script | Pro spouštění skriptů by měl být vlastníkem souboru "root" uživatel a soubor by měl mít oprávnění "700" (to znamená, že pouze "vlastník" by měl mít oprávnění "číst", "psát" a "spustit"). | Ujistěte se, že "root" uživatel je "vlastník" souboru skriptu a že pouze "vlastník" má oprávnění "číst", "psát" a "spustit". |
| InsufficientPermissionforPost-Script InsufficientPermissionforPost-Script InsufficientPermissionforPost-Script InsufficientPermission | Pro spouštění skriptů by měl být správcem souboru uživatel kořenového adresáře a soubor by měl mít oprávnění "700" (to znamená, že pouze "vlastník" by měl mít oprávnění "číst", "psát" a "spustit"). | Ujistěte se, že "root" uživatel je "vlastník" souboru skriptu a že pouze "vlastník" má oprávnění "číst", "psát" a "spustit". |
| Časový čas před skriptem | Provádění zálohy konzistentní aplikace pre-script časový rozsah. | Zkontrolujte skript a zvyšte časový čas v souboru **VMSnapshotScriptPluginConfig.json,** který je umístěn na **adrese /etc/azure**. |
| Časový čas po skriptu | Provádění zálohování konzistentní aplikace post-script časový plán. | Zkontrolujte skript a zvyšte časový čas v souboru **VMSnapshotScriptPluginConfig.json,** který je umístěn na **adrese /etc/azure**. |

## <a name="next-steps"></a>Další kroky

[Konfigurace zálohování virtuálních počítače do trezoru služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
