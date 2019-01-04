---
title: 'Azure Backup: konzistentní zálohování virtuálních počítačů s Linuxem'
description: Vytvoření zálohy konzistentní s aplikací z vašich virtuálních počítačů s Linuxem do Azure. Tento článek vysvětluje, konfiguraci rozhraní skript pro zálohování Azure nasazené virtuální počítače s Linuxem. Tento článek také obsahuje informace o odstraňování potíží.
services: backup
author: anuragmehrotra
manager: shivamg
keywords: zálohování konzistentní s aplikací; konzistentní zálohování virtuálních počítačů Azure; Zálohování virtuálního počítače s Linuxem; Azure Backup
ms.service: backup
ms.topic: conceptual
ms.date: 1/12/2018
ms.author: anuragm
ms.openlocfilehash: a81c0b9c87db85771fcecab87c6b9ac88dcbd472
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581840"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Konzistentní zálohování virtuálních počítačů Azure s Linuxem

Při přijímání snímky se zálohami virtuálních počítačů, konzistence aplikace znamená, že vaše aplikace spustit při spuštění virtuálních počítačů, po který se má obnovit. Konzistentnost aplikací si dokážete představit, je velmi důležité. K zajištění jsou vaše virtuální počítače s Linuxem konzistentní, že můžete použít rozhraní Linux předsnímkových a posnímkových skriptů konzistentní zálohování s aplikací. Rozhraní předzálohovacími a pozálohovacími skripty podporuje virtuální počítače s Linuxem nasazené Azure Resource Manageru. Skripty pro zajištění konzistence aplikace nepodporují portálu Service Manager nasadit virtuální počítače nebo virtuální počítače Windows.

## <a name="how-the-framework-works"></a>Jak funguje rozhraní framework

Rozhraní poskytuje možnost spouštět skripty před a po skripty, zatímco přenášíte snímky virtuálních počítačů. Předběžné skripty se spouští jenom dříve, než se snímek virtuálního počítače a pozálohovacích skriptů spustit ihned po pořízení snímku virtuálního počítače. Předběžné skripty a pozálohovacích skriptů poskytují flexibilitu pro řízení svoji aplikaci a prostředí, zatímco přenášíte snímky virtuálních počítačů.

Předběžné skripty vyvolat nativní aplikace rozhraní API, které uvedení IOs a vyprázdnit obsah v paměti na disk. Tyto akce Ujistěte se, že je snímek konzistentní s aplikací. Pozálohovacích skriptů pomocí nativní aplikace rozhraní API se uvolnit pro IOs, které umožní aplikaci obnovit normální provoz po snímek virtuálního počítače.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Postup konfigurace předsnímkových a posnímkových skriptů

1. Přihlaste se jako uživatel root do virtuálního počítače s Linuxem, které chcete zálohovat.

2. Z [Githubu](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), stáhněte si **VMSnapshotScriptPluginConfig.json** a zkopírujte ho do **/etc/azure** složky pro všechny virtuální počítače, které chcete zálohovat. Pokud **/etc/azure** složka neexistuje, vytvořte ji.

3. Zkopírujte předzálohovacími a pozálohovacími skripty pro vaši aplikaci pro všechny virtuální počítače, které máte v plánu zálohování. Skripty můžete zkopírovat do jakéhokoli umístění na virtuálním počítači. Nezapomeňte aktualizovat úplnou cestu souboru skriptu v **VMSnapshotScriptPluginConfig.json** souboru.

4. Ověřte následující oprávnění pro tyto soubory:

   - **VMSnapshotScriptPluginConfig.json**: Oprávnění "600". Například pouze "root" uživatel by měl mít oprávnění "číst" a "write" k tomuto souboru a uživatel by měl mít oprávnění "spustit".

   - **Předzálohovací skript soubor**: Oprávnění "700".  Například by měl mít pouze "root" uživatel "čtení", "write" a "spustit" k tomuto souboru oprávnění.

   - **Pozálohovací skript** oprávnění "700". Například by měl mít pouze "root" uživatel "čtení", "write" a "spustit" k tomuto souboru oprávnění.

   > [!Important]
   > Rozhraní poskytuje uživatelům velké množství energie. Zabezpečení rozhraní framework a ujistěte se, že pouze "root" uživatel má přístup k kritické JSON a soubory skriptů.
   > Pokud požadavky nejsou splněny, skript se nespustí, výsledkem je selhání systému souborů a nekonzistentní záloha.
   >

5. Konfigurace **VMSnapshotScriptPluginConfig.json** podle postupu popsaného tady:
    - **pluginName**: Ponechte toto pole je nebo skriptů nemusí fungovat podle očekávání.

    - **preScriptLocation**: Zadejte úplnou cestu předzálohovacího skriptu ve virtuálním počítači, na který budete zálohovat.

    - **postScriptLocation**: Zadejte úplnou cestu pozálohovací skript na virtuálním počítači, který se bude zálohovat.

    - **preScriptParams**: Zadejte volitelné parametry, které je potřeba předat předzálohovacího skriptu. Všechny parametry musí být v uvozovkách. Pokud používáte více parametrů, parametry oddělte čárkou.

    - **postScriptParams**: Zadejte volitelné parametry, které je potřeba předat pozálohovací skript. Všechny parametry musí být v uvozovkách. Pokud používáte více parametrů, parametry oddělte čárkou.

    - **preScriptNoOfRetries**: Nastavte počet pokusů, které se předzálohovací skript by měl zopakuje, pokud dojde k jakékoli chybě před ukončením. Nula znamená, že pouze jeden pokus a nebude opakovat, pokud dojde k selhání.

    - **postScriptNoOfRetries**:  Nastavte počet pokusů, které se pozálohovací skript by měl zopakuje, pokud dojde k jakékoli chybě před ukončením. Nula znamená, že pouze jeden pokus a nebude opakovat, pokud dojde k selhání.

    - **časový_limit_v_sekundách**: Zadejte jednotlivé vypršení časových limitů u předzálohovací skript a pozálohovací skript (maximální hodnota může být 1 800).

    - **continueBackupOnFailure**: Nastavte tuto hodnotu na **true** Pokud chcete Azure Backup vrátit k souboru systému konzistentní vzhledem k aplikacím/selhání záloha konzistentní v případě, pokud předzálohovacího skriptu nebo se nezdaří pro provedení pozálohovacího skriptu. Nastavení na **false** selže zálohování v případě selhání skriptu (s výjimkou případů, kdy máte jeden disk virtuálního počítače, který spadne zpět na konzistentní zálohování bez ohledu na toto nastavení).

    - **fsFreezeEnabled**: Určete, zda Linux fsfreeze by měla být volána, když jste pořízení snímku virtuálního počítače k zajištění konzistence systému souborů. Doporučujeme tuto možnost nastavenou na **true** Pokud vaše aplikace má závislost na zakázání fsfreeze.

6. Rozhraní skriptu je nyní nakonfigurována. Pokud je zálohování virtuálního počítače je už nakonfigurovaná, dalším zálohování vyvolá skripty a aktivuje zálohování konzistentní s aplikací. Pokud je zálohování virtuálního počítače není nakonfigurovaná, nakonfigurovat ji pomocí [zálohovat virtuální počítače Azure do trezorů služby Recovery Services.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Řešení potíží

Ujistěte se, že přidáte odpovídající protokolování při zápisu předsnímkových a posnímkových skriptů a zkontrolujte protokoly skriptu opravit případné problémy skriptu. Pokud stále máte problémy se spouštěním skriptů, najdete v následující tabulce najdete další informace.

| Chyba | Chybová zpráva | Doporučená akce |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Předzálohovací skript vrátil chybu, takže záloha nemusí být konzistentní s aplikací.   | Podívejte se na protokoly chyb vašeho skriptu, pokud chcete problém vyřešit.|  
|   ScriptExecutionFailed příspěvku |    Pozálohovací skript vrátil chybu, která může mít vliv na stav aplikace. |    Podívejte se na protokoly chyb vašeho skriptu, abyste tento problém vyřešit a zkontrolovat stav aplikace. |
| Pre-ScriptNotFound |  Najít předzálohovací skript v umístění zadaném v **VMSnapshotScriptPluginConfig.json** konfiguračního souboru. |   Zkontrolujte, že to předzálohovací skript nachází na cestu, která je určena v konfiguračním souboru, aby zálohování konzistentní s aplikací.|
| ScriptNotFound příspěvku | Najít pozálohovací skript v umístění zadaném v **VMSnapshotScriptPluginConfig.json** konfiguračního souboru. |   Zkontrolujte, že to pozálohovací skript nachází na cestě zadané v konfiguračním souboru, aby zálohování konzistentní s aplikací.|
| IncorrectPluginhostFile | **Pluginhost** soubor, který je součástí rozšíření VmSnapshotLinux, je poškozený, takže předzálohovací a pozálohovací skript nejde spustit, nebude záloha konzistentní s aplikací. | Odinstalace **VmSnapshotLinux** rozšíření a bude automaticky znovu při dalším zálohování se tento problém vyřešit. |
| IncorrectJSONConfigFile | **VMSnapshotScriptPluginConfig.json** souboru je nesprávná, takže předzálohovací skript a pozálohovací skript nejde spustit, nebude záloha konzistentní s aplikací. | Stáhněte si kopii z [Githubu](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) a konfigurovat znova. |
| InsufficientPermissionforPre-Script | Ke spouštění skriptů, "root" uživatel by měl být vlastníka souboru a soubor by měl mít oprávnění "700" (to znamená, že by měl mít pouze "vlastník" "čtení", "write" a "spustit" oprávnění). | Ujistěte se, že uživatel "root" je "vlastník" soubor skriptu a že pouze "vlastník" je "oprávnění ke čtení", "write" a "spustit". |
| InsufficientPermissionforPost-Script | Ke spouštění skriptů, musí být uživatel root vlastníka souboru a soubor by měl mít oprávnění "700" (to znamená, že by měl mít pouze "vlastník" "čtení", "write" a "spustit" oprávnění). | Ujistěte se, že uživatel "root" je "vlastník" soubor skriptu a že pouze "vlastník" je "oprávnění ke čtení", "write" a "spustit". |
| Pre-ScriptTimeout | Spuštění konzistentní zálohování předzálohovacího skriptu vypršel časový limit. | Zkontrolujte skript a zvýšit časový limit v **VMSnapshotScriptPluginConfig.json** soubor, který se nachází ve **/etc/azure**. |
| ScriptTimeout příspěvku | Vypršel časový limit spuštění konzistentní zálohování pozálohovací skript. | Zkontrolujte skript a zvýšit časový limit v **VMSnapshotScriptPluginConfig.json** soubor, který se nachází ve **/etc/azure**. |

## <a name="next-steps"></a>Další postup
[Konfigurace zálohování virtuálních počítačů do trezoru služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
