---
title: 'Azure Backup: zálohování konzistentní s aplikací virtuálních počítačů Linux'
description: Vytvořte zálohování konzistentní s aplikací virtuálních počítačů Linux do Azure. Tento článek vysvětluje, konfiguraci rozhraní skript pro zálohování Azure nasazené virtuální počítače s Linuxem. Tento článek obsahuje také informace o odstraňování potíží.
services: backup
author: anuragmehrotra
manager: shivamg
keywords: zálohování konzistentní s aplikací; zálohování konzistentní s aplikací virtuálního počítače Azure; Zálohování virtuálních počítačů Linux; Zálohování Azure
ms.service: backup
ms.topic: conceptual
ms.date: 1/12/2018
ms.author: anuragm
ms.openlocfilehash: 027fc4098e7760de276a8548453bb83599ed0521
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605208"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Zálohování konzistentní s aplikací Azure Linux virtuálních počítačů

Při pořizování snímků záloh virtuálních počítačů, konzistence aplikací znamená, že vaše aplikace spustit v případě, že virtuální počítače spustit po obnovena. Jak si lze představit, je velmi důležité konzistence aplikace. Aby virtuální počítače Linux jsou aplikace konzistentní že rozhraní skript před a po skript Linux můžete provést zálohování konzistentní s aplikací. Rozhraní framework skript před a po skript podporuje virtuální počítače s Linuxem nasazení Azure Resource Manager. Skripty pro konzistence aplikací nepodporují nasazení portálu Service Manager virtuální počítače nebo virtuální počítače s Windows.

## <a name="how-the-framework-works"></a>Jak funguje rozhraní

Rozhraní framework poskytuje možnost spustit vlastní skripty před a po skriptů při přenášíte snímky virtuálních počítačů. Spustit předběžné skripty těsně před provedením snímku virtuálního počítače a po skripty spustit okamžitě po pořídíte snímek virtuálního počítače. Před a po skriptů poskytují flexibilitu pro řízení aplikací a prostředí při přenášíte snímky virtuálních počítačů.

Předběžné skripty vyvolání nativní aplikace rozhraní API, které uvedení IOs a vyprázdnit obsah v paměti na disk. Tyto akce Ujistěte se, že snímek je aplikace, které jsou konzistentní. Po skripty pomocí nativní aplikace rozhraní API se odblokování IOs, které umožní aplikaci obnovit normální provozní podmínky po snímek virtuálního počítače.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Postup konfigurace skript před a po skript

1. Přihlaste se jako kořenový uživatel do virtuálního počítače s Linuxem, které chcete zálohovat.

2. Z [Githubu](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), stáhněte si **VMSnapshotScriptPluginConfig.json** a zkopírujte ho do **/etc/azure** složky pro všechny virtuální počítače, které chcete zálohovat. Pokud **/etc/azure** složka neexistuje, vytvořte ho.

3. Zkopírujte skript před a po skript pro vaši aplikaci na všech virtuálních počítačích, které chcete zálohovat. Skripty můžete zkopírovat do libovolného umístění, do virtuálního počítače. Nezapomeňte aktualizovat úplnou cestu souborů skriptů v **VMSnapshotScriptPluginConfig.json** souboru.

4. Zkontrolujte následující oprávnění pro tyto soubory:

   - **VMSnapshotScriptPluginConfig.json**: oprávnění "600." Například pouze "root" uživatel by měl mít oprávnění "číst" a "zápisu" k tomuto souboru a žádný uživatel by měl mít oprávnění "spustit".

   - **Soubor skriptu před**: oprávnění "700."  Například by měl mít jenom uživatel "root" "číst", "zápisu" a "spustit" oprávnění k tomuto souboru.
  
   - **Po skriptu** oprávnění "700." Například by měl mít jenom uživatel "root" "číst", "zápisu" a "spustit" oprávnění k tomuto souboru.

   > [!Important]
   > Rozhraní framework poskytuje uživatelům spoustu napájení. Zabezpečení rozhraní a ujistěte se, že pouze "root" uživatel má přístup k kritické JSON a soubory skriptu.
   > Pokud požadavky nejsou splněny, skript se nespustí, výsledkem je selhání systému souborů a konzistentní zálohování.
   >

5. Konfigurace **VMSnapshotScriptPluginConfig.json** podle postupu popsaného tady:
    - **pluginName**: ponechat toto pole, jako je nebo skripty nemusí fungovat podle očekávání.

    - **preScriptLocation**: zadat úplnou cestu předběžné skriptu ve virtuálním počítači, který se bude zálohovat.

    - **postScriptLocation**: zadat úplnou cestu po skriptu ve virtuálním počítači, který se bude zálohovat.

    - **preScriptParams**: Zadejte volitelné parametry, které je třeba předávané předběžné skriptu. Všechny parametry musí být v uvozovkách. Pokud chcete použít několik parametrů, oddělte čárkou parametry.

    - **postScriptParams**: Zadejte volitelné parametry, které je třeba předat po skriptu. Všechny parametry musí být v uvozovkách. Pokud chcete použít několik parametrů, oddělte čárkou parametry.

    - **preScriptNoOfRetries**: nastavte počet pokusů před skript by měl opakovat, pokud chybě před ukončením. Zkuste pouze jeden nula znamená a žádné opakování. Pokud dojde k selhání.

    - **postScriptNoOfRetries**: nastavit počet opakování po skript by měl být pokud chybě před ukončením. Zkuste pouze jeden nula znamená a žádné opakování. Pokud dojde k selhání.
    
    - **časový_limit_v_sekundách**: Zadejte jednotlivé časové limity pro předběžné skript a po skript.

    - **continueBackupOnFailure**:, nastavte hodnotu **true** Pokud budete chtít Azure Backup vrátit k konzistentní nebo havárie konzistentní zálohu systému souborů, pokud skript před nebo po skript selže. Toto nastavení na **false** nezdaří zálohování v případě selhání skriptu (s výjimkou případů, kdy máte jeden disk virtuálního počítače, který se vrátí k konzistentní při selhání zálohování bez ohledu na toto nastavení).

    - **fsFreezeEnabled**: Zadejte, zda Linux fsfreeze by měla být volána, když jste pořízení snímku virtuálního počítače k zajištění konzistence systému souborů. Doporučujeme zachovat tohoto nastavení nastavena na **true** Pokud vaše aplikace je závislý na zakázání fsfreeze.

6. Rozhraní framework skriptu je nyní nakonfigurována. Pokud už je nakonfigurované zálohování virtuálních počítačů, další zálohování vyvolá skripty a aktivuje zálohování konzistentní s aplikací. Pokud není nakonfigurováno zálohování virtuálních počítačů, nakonfigurovat ji pomocí [zálohovat virtuální počítače, které jsou k trezory služeb zotavení Azure.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Řešení potíží

Ujistěte se, že přidáte příslušné protokolování při psaní skriptů před a po skriptu a kontrole skriptu protokolů k opravte všechny problémy skriptu. Pokud máte potíže se spouštěním skriptů, naleznete další informace v následující tabulce.

| Chyba | Chybová zpráva | Doporučená akce |
| ------------------------ | -------------- | ------------------ |
| Před ScriptExecutionFailed |Předběžné skript vrátila chybu, takže záloha pravděpodobně nebude konzistentní s aplikací.   | Podívejte se na protokoly selhání vašeho skriptu vyřešit problém.|  
|   POST-ScriptExecutionFailed |    Po skript vrátí chybu, která může mít vliv na stav aplikace. |    Podívejte se na protokoly selhání vašeho skriptu, opravte problém a zkontrolujte stav aplikace. |
| Před ScriptNotFound |  Předběžné skript nebyl nalezen v umístění, který je uveden v **VMSnapshotScriptPluginConfig.json** konfiguračního souboru. |   Zkontrolujte, jestli to před skript se nachází v cestě, který je uveden v konfiguračním souboru, aby zálohování konzistentní s aplikací.|
| POST-ScriptNotFound | Po skript nebyl nalezen v umístění, který je uveden v **VMSnapshotScriptPluginConfig.json** konfiguračního souboru. |   Zkontrolujte, že to po skript se nachází v cestě, který je uveden v konfiguračním souboru, aby zálohování konzistentní s aplikací.|
| IncorrectPluginhostFile | **Pluginhost** souboru, který se dodává s příponou VmSnapshotLinux, je poškozený, takže nelze spustit skript před a po skript a zálohování nebude konzistentní s aplikací. | Odinstalace **VmSnapshotLinux** rozšíření a bude nutné přeinstalovat automaticky s další zálohování na opravě problému. |
| IncorrectJSONConfigFile | **VMSnapshotScriptPluginConfig.json** souboru je nesprávná, takže před skript a po skriptu nelze spustit a zálohování nebude konzistentní s aplikací. | Stáhněte si kopii z [Githubu](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) a nakonfigurujte ji znovu. |
| InsufficientPermissionforPre skriptu | Ke spouštění skriptů, "root" uživatel by měl být vlastníka souboru a soubor musí mít oprávnění "700" (to znamená, musí mít pouze "vlastník" "číst", "zápisu" a "spustit" oprávnění). | Zajistěte, aby uživatel "root" se "vlastník" soubor skriptu a že pouze "vlastník" má "oprávnění ke čtení", "zápisu" a "spustit". |
| InsufficientPermissionforPost skriptu | Ke spouštění skriptů, kořenový uživatel by měl být vlastníka souboru a soubor musí mít oprávnění "700" (to znamená, musí mít pouze "vlastník" "číst", "zápisu" a "spustit" oprávnění). | Zajistěte, aby uživatel "root" se "vlastník" soubor skriptu a že pouze "vlastník" má "oprávnění ke čtení", "zápisu" a "spustit". |
| Před ScriptTimeout | Doba pro provedení konzistentní s aplikací zálohování před skriptu vypršel. | Zkontrolujte skript a zvýšit časový limit v **VMSnapshotScriptPluginConfig.json** soubor, který se nachází v **/etc/azure**. |
| POST-ScriptTimeout | Vypršel časový limit spuštění zálohování konzistentní s aplikací po skriptu. | Zkontrolujte skript a zvýšit časový limit v **VMSnapshotScriptPluginConfig.json** soubor, který se nachází v **/etc/azure**. |

## <a name="next-steps"></a>Další postup
[Konfigurace zálohování virtuálních počítačů do trezoru služeb zotavení](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
