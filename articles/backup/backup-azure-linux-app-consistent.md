---
title: Zálohy virtuálních počítačů se systémem Linux konzistentní vzhledem k aplikacím
description: Vytvářejte zálohy virtuálních počítačů s konzistentním vzhledem k aplikacím do Azure. V tomto článku se dozvíte, jak nakonfigurovat rozhraní skriptů pro zálohování virtuálních počítačů Linux nasazených v Azure. Tento článek také obsahuje informace o řešení potíží.
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 22053004026a2dd8976027359f11d50a5663b334
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88999236"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Zálohování virtuálních počítačů Azure s Linuxem konzistentní vzhledem k aplikacím

Při pořizování záložních snímků virtuálních počítačů to znamená, že se vaše aplikace spustí, když se po obnovení spustí virtuální počítače. Jak si představit, konzistence aplikací je mimořádně důležitá. Aby bylo zajištěno, že vaše virtuální počítače se systémem Linux jsou konzistentní vzhledem k aplikacím, můžete k použití zálohování konzistentního vzhledem k aplikacím použít rozhraní předzálohovacího skriptu pro Linux a post-Script. Rozhraní předzálohovacích a pozálohovacích skriptů podporuje Azure Resource Manager nasazených virtuálních počítačů se systémem Linux. Skripty pro konzistenci aplikací nepodporují Service Manager nasazených virtuálních počítačů nebo virtuálních počítačů s Windows.

## <a name="how-the-framework-works"></a>Jak architektura funguje

Rozhraní poskytuje možnost spustit vlastní předzálohovací skripty a následné skripty při přebírání snímků virtuálních počítačů. Před spuštěním snímku virtuálního počítače se spustí předzálohovací skript a po provedení snímku virtuálního počítače se spustí následné skripty. Předzálohovací a pozálohovací skripty poskytují flexibilitu při řízení aplikace a prostředí při přebírání snímků virtuálních počítačů.

Předběžné skripty vyvolávají nativní aplikační rozhraní API, které neobsahují operační systém IOs a vyprázdní obsah v paměti na disk. Tyto akce zajišťují, že je snímek konzistentní vzhledem k aplikacím. Následné skripty používají rozhraní API nativních aplikací k odblokování IOs, které umožní aplikaci obnovit normální provoz po snímku virtuálního počítače.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Postup konfigurace předzálohovacího skriptu a následného skriptu

1. Přihlaste se jako kořenový uživatel k virtuálnímu počítači se systémem Linux, který chcete zálohovat.

2. Z [GitHubu](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)stáhněte **VMSnapshotScriptPluginConfig.js** a zkopírujte ho do složky **složce/etc/Azure** pro všechny virtuální počítače, které chcete zálohovat. Pokud složka **složce/etc/Azure** neexistuje, vytvořte ji.

3. Zkopírujte předzálohovací skript a pozálohovací skript pro vaši aplikaci na všech virtuálních počítačích, které chcete zálohovat. Skripty můžete zkopírovat do libovolného umístění na VIRTUÁLNÍm počítači. Nezapomeňte aktualizovat úplnou cestu souborů skriptu v **VMSnapshotScriptPluginConfig.js** souboru.

4. Zajistěte pro tyto soubory následující oprávnění:

   - **VMSnapshotScriptPluginConfig.js**: oprávnění "600". Například pouze "root" uživatel musí mít oprávnění číst a zapsat k tomuto souboru a žádný uživatel by neměl mít oprávnění EXECUTE.

   - **Soubor předzálohovacího skriptu**: oprávnění "700".  Například pouze "root" uživatel musí mít oprávnění "číst", "zapsat" a "spustit" pro tento soubor.

   - **Po skriptu** Oprávnění "700". Například pouze "root" uživatel musí mít oprávnění "číst", "zapsat" a "spustit" pro tento soubor.

   > [!IMPORTANT]
   > Rozhraní poskytuje uživatelům spoustu výkonu. Zabezpečte rozhraní a zajistěte, aby měl uživatel root přístup k důležitým souborům JSON a Script.
   > Pokud nejsou požadavky splněné, skript se nespustí, což vede k selhání systému souborů a nekonzistentnímu zálohování.
   >

5. Nakonfigurujte **VMSnapshotScriptPluginConfig.js** , jak je popsáno zde:
    - **modul plug-in**: nechte toto pole jako, nebo vaše skripty nemusí fungovat podle očekávání.

    - **preScriptLocation**: zadejte úplnou cestu předzálohovacího skriptu na virtuálním počítači, který se bude zálohovat.

    - **postScriptLocation**: zadejte úplnou cestu pozálohovacího skriptu na virtuálním počítači, který se bude zálohovat.

    - **preScriptParams**: zadejte volitelné parametry, které je třeba předat předzálohovacímu skriptu. Všechny parametry by měly být v uvozovkách. Pokud použijete více parametrů, oddělte parametry čárkou.

    - **postScriptParams**: zadejte volitelné parametry, které je třeba předat skriptu po odeslání. Všechny parametry by měly být v uvozovkách. Pokud použijete více parametrů, oddělte parametry čárkou.

    - **preScriptNoOfRetries**: nastavte počet opakovaných pokusů o opakování skriptu, pokud před ukončením dojde k chybě. Nula znamená pouze jeden pokus a bez opakování, pokud dojde k selhání.

    - **postScriptNoOfRetries**: nastavte počet opakovaných pokusů, které by se měl opakovat po ukončení skriptu, pokud dojde k chybě. Nula znamená pouze jeden pokus a bez opakování, pokud dojde k selhání.

    - **timeoutInSeconds**: zadejte jednotlivé časové limity pro předzálohovací skript a po skript (maximální hodnota může být 1800).

    - **continueBackupOnFailure**: tuto hodnotu nastavte na **true** , pokud chcete, aby se Azure Backup přechodem k zálohování konzistentnímu se systémem souborů nebo selháním v případě, že se nepovede nebo pozálohovací skript selže. Když se tato možnost nastaví na **false** , zálohování selže, pokud dojde k selhání skriptu (kromě případu, kdy máte virtuální počítač s jedním diskem, který se vrátí do zálohy konzistentní bez ohledu na toto nastavení). Pokud je hodnota **continueBackupOnFailure** nastavena na hodnotu false, bude pokus o zálohování znovu proveden na základě logiky opakování ve službě (pro stanovený počet pokusů).

    - **fsFreezeEnabled**: Určete, jestli by se měla volat Linux fsfreeze při přebírání snímku virtuálního počítače, aby se zajistila konzistence systému souborů. Doporučujeme ponechat toto nastavení nastavené na **hodnotu true** , pokud vaše aplikace nezávisí na zakázání fsfreeze.

    - **ScriptsExecutionPollTimeSeconds**: Nastavte dobu, po kterou má rozšíření při každém cyklickém dotazování na spuštění skriptu přejít do režimu spánku. Například pokud je hodnota 2, rozšíření ověří, zda bylo provedeno předběžné/následné spuštění skriptu každé 2 sekundy. Minimální a maximální hodnota, kterou může trvat, je 1 a 5 v uvedeném pořadí. Hodnota by měla být výhradně celé číslo.

6. Rozhraní Script Framework je nyní nakonfigurováno. Pokud je zálohování virtuálního počítače už nakonfigurované, další záloha vyvolá skripty a spustí zálohování konzistentní s aplikacemi. Pokud zálohování virtuálního počítače není nakonfigurované, nakonfigurujte ho pomocí [zálohování virtuálních počítačů Azure do trezorů Recovery Services.](./backup-azure-vms-first-look-arm.md)

## <a name="troubleshooting"></a>Řešení potíží

Nezapomeňte přidat vhodné protokolování při psaní předzálohovacího skriptu a následného skriptu a zkontrolujte protokoly skriptu a opravte případné problémy se skripty. Pokud stále máte problémy se spouštěním skriptů, další informace najdete v následující tabulce.

| Chyba | Chybová zpráva | Doporučená akce |
| ------------------------ | -------------- | ------------------ |
| Před ScriptExecutionFailed |Předzálohovací skript vrátil chybu, takže záloha nemusí být konzistentní vzhledem k aplikacím.| Pokud chcete problém vyřešit, podívejte se do protokolů o selhání pro váš skript.|  
|Po ScriptExecutionFailed |Pozálohovací skript vrátil chybu, která může ovlivnit stav aplikace. |Pokud chcete problém vyřešit a zkontrolovat stav aplikace, podívejte se na protokoly selhání pro váš skript. |
| Před ScriptNotFound |Předzálohovací skript nebyl nalezen v umístění, které je zadáno v **VMSnapshotScriptPluginConfig.js** konfiguračního souboru. |Ujistěte se, že se předzálohovací skript nachází na cestě zadané v konfiguračním souboru, aby se zajistila záloha konzistentní vzhledem k aplikacím.|
| Po ScriptNotFound |Pozálohovací skript se nenašel v umístění, které je zadané v **VMSnapshotScriptPluginConfig.js** konfiguračního souboru. |Ujistěte se, že se pozálohovací skript nachází na cestě zadané v konfiguračním souboru, aby se zajistila záloha konzistentní vzhledem k aplikacím.|
| IncorrectPluginhostFile |Soubor **Pluginhost** , který se dodává s rozšířením VmSnapshotLinux, je poškozený, takže předzálohovací a pozálohovací skript nejde spustit a záloha nebude konzistentní vzhledem k aplikacím.| Odinstalujte rozšíření **VmSnapshotLinux** a automaticky se znovu nainstaluje s další zálohou, aby se problém vyřešil. |
| IncorrectJSONConfigFile | **VMSnapshotScriptPluginConfig.jsv** souboru je nesprávný, takže nejde spustit předzálohovací a pozálohovací skript a záloha nebude konzistentní vzhledem k aplikacím. | Stáhněte si kopii z [GitHubu](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) a znovu ji nakonfigurujte. |
| InsufficientPermissionforPre-Script | Pro spouštění skriptů by měl být uživatel "root" vlastníkem souboru a soubor by měl mít oprávnění "700" (to znamená, že musí mít oprávnění číst, zapisovat a spustit). | Ujistěte se, že uživatel root je vlastníkem souboru skriptu a že má oprávnění číst, zapisovat a spustit pouze "vlastník". |
| InsufficientPermissionforPost-Script | Pro spouštění skriptů by měl být kořenový uživatel vlastníkem souboru a tento soubor by měl mít oprávnění "700" (to znamená, že musí mít oprávnění číst, zapisovat a spustit). | Ujistěte se, že uživatel root je vlastníkem souboru skriptu a že má oprávnění číst, zapisovat a spustit pouze "vlastník". |
| Pre-ScriptTimeout | Vypršel časový limit pro spuštění předzálohovacího skriptu zálohování konzistentního vzhledem k aplikacím. | Podívejte se na skript a zvyšte časový limit v **VMSnapshotScriptPluginConfig.js** souboru, který se nachází na adrese **složce/etc/Azure**. |
| Po-ScriptTimeout | Vypršel časový limit pro spuštění pozálohovacího skriptu zálohování konzistentního vzhledem k aplikacím. | Podívejte se na skript a zvyšte časový limit v **VMSnapshotScriptPluginConfig.js** souboru, který se nachází na adrese **složce/etc/Azure**. |

## <a name="next-steps"></a>Další kroky

[Konfigurace zálohování virtuálních počítačů do trezoru Recovery Services](./backup-azure-vms-first-look-arm.md)
