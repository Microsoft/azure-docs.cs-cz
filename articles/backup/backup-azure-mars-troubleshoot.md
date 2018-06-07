---
title: Řešení potíží s agentem Azure Backup
description: Odstraňování problémů instalace a registrace agenta Azure Backup
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 12/4/2017
ms.author: saurse
ms.openlocfilehash: aee0a3044ea4d1b9b867e795e94a37f8835ad212
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605752"
---
# <a name="troubleshoot-azure-backup-agent-configuration-and-registration-issues"></a>Řešení potíží s Azure Backup Agent konfigurace a registrace problémy
## <a name="recommended-steps"></a>Doporučené kroky
Naleznete v následujících tabulkách vyřešit chyby, které můžete narazit při konfiguraci a registraci agenta Azure Backup pomocí doporučených akcí.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Neplatné přihlašovací údaje úložiště. Soubor je buď poškozený nebo nemá mít poslední přihlašovací údaje přísluší službě obnovení.
| Detaily chyby | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |
| **Chyba** </br> *Neplatné přihlašovací údaje úložiště. Soubor je buď poškozený nebo nemá mít poslední přihlašovací údaje přísluší službě obnovení. (ID: 34513)* | <ul><li> Přihlašovací údaje úložiště jsou neplatné (to znamená, že byly staženy víc než 48 hodin, než čas registrace).<li>Není možné stáhnout dočasný soubor ke složce Temp systému Windows Azure Backup Agent. <li>Přihlašovací údaje úložiště jsou v umístění v síti. <li>Protokol TLS 1.0 je zakázána.<li> Konfigurovaný server proxy server blokuje připojení. <br> |  <ul><li>Stáhněte nové přihlašovací údaje trezoru.<li>Přejděte na **Možnosti Internetu** > **zabezpečení** > **Internet**. Potom vyberte **vlastní úroveň**a pomocí posuvníku vyhledejte soubor stáhnout části. Potom vyberte **povolit**.<li>Budete také muset přidejte tento web do [Důvěryhodné servery](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Změňte nastavení na použití proxy serveru. Zadejte proxy server podrobnosti serveru. <li> Datum a čas odpovídat svůj počítač.<li>Přejděte do C:/Windows/Temp a zkontrolujte, zda jsou více než 60 000 nebo 65 000 soubory s příponou TMP. Pokud existuje, odstraňte tyto soubory.<li>Toto můžete otestovat spuštěním SDP balíčku na serveru. Pokud se chyba oznamující, že stahování souborů nejsou povoleny, je velmi pravděpodobné, že jsou velký počet souborů v adresáři C:/Windows/Temp.<li>Ujistěte se, že máte nainstalováno rozhraní .NET framework 4.6.2. <li>Pokud jste zakázali TLS 1.0 z důvodu soulad s normami PCI, podívejte se na to [řešení potíží s stránky](https://support.microsoft.com/help/4022913). <li>Pokud máte antivirový software nainstalovaný na serveru, vylučte z kontroly antivirový následující soubory: <ul><li>CBengine.exe<li>CSC.exe, který má vztah k rozhraní .NET Framework. Není CSC.exe pro každou verzi rozhraní .NET, který je nainstalován na serveru. Vylučte soubory CSC.exe, které jsou svázané s všechny verze rozhraní .NET framework na příslušném serveru. <li>Cesta k pomocnému složku nebo mezipaměti v umístění. <br>*Výchozí umístění pro pomocné složka nebo cesta k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent služeb zotavení Microsoft Azure se nepodařilo připojit k Microsoft Azure Backup

| Detaily chyby | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |
| **Chyba** </br><ol><li>*Agent služeb zotavení Microsoft Azure se nepodařilo připojit k Microsoft Azure Backup. (ID: 100050) Zkontrolujte síťové nastavení a ujistěte se, že budete moci připojit k Internetu*<li>*Vyžadováno ověření proxy serveru (407)* |Proxy Server blokuje připojení. |  <ul><li>Přejděte do **Možnosti Internetu** > **zabezpečení** > **Internet**. Potom vyberte **vlastní úroveň** a posuvníku vyhledejte soubor stáhnout části. Vyberte **Povolit**.<li>Budete také muset přidejte tento web do [Důvěryhodné servery](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Změňte nastavení na použití proxy serveru. Zadejte proxy server podrobnosti serveru. <li>Pokud máte antivirový software nainstalovaný na serveru, vylučte z kontroly antivirový následující soubory. <ul><li>CBEngine.exe (namísto dpmra.exe).<li>CSC.exe (týkající se rozhraní .NET Framework). Není CSC.exe pro každou verzi rozhraní .NET, který je nainstalován na serveru. Vylučte soubory CSC.exe, které jsou svázané s všechny verze rozhraní .NET framework na příslušném serveru. <li>Cesta k pomocnému složku nebo mezipaměti v umístění. <br>*Výchozí umístění pro pomocné složka nebo cesta k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nepodařilo se nastavit šifrovací klíč pro zabezpečené zálohy

| Detaily chyby | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |      
| **Chyba** </br>*Nepodařilo se nastavit šifrovací klíč pro zabezpečené zálohy. Aktuální operace selhala kvůli vnitřní chyba: Neplatné vstupní chybě služby'. Opakujte operaci po určité době. Pokud potíže potrvají, kontaktujte prosím podporu společnosti Microsoft*. |Server je již zaregistrována k jiné trezoru.| Zrušte registraci serveru z trezoru a znovu zaregistrujte.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>Aktivace nebyla úspěšně dokončena. Aktuální operace selhala kvůli vnitřní chybě služby [0x1FC07]

| Detaily chyby | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |          
| **Chyba** </br><ol><li>*Aktivace nebyla úspěšně dokončena. Aktuální operace selhala kvůli vnitřní chybě služby [0x1FC07]. Opakujte operaci po určité době. Pokud potíže potrvají, kontaktujte prosím podporu společnosti Microsoft* <li>*Došlo k chybě 34506. Šifrovací přístupové heslo uložené v tomto počítači není správně nakonfigurováno*. | <li> Pracovní složka je umístěna ve svazku, který nemá dostatek místa. <li> Pracovní složky přesunula nesprávně do jiného umístění. <li> OnlineBackup.KEK soubor nebyl nalezen. | <li>Přesuňte pomocné složku nebo umístění mezipaměti na svazek s ekvivalent hodnoty 5 až 10 % volného místa a celkové velikosti zálohovaná data. Správně přesunout umístění mezipaměti, naleznete postup v [dotazy týkající se Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Ujistěte se, že je soubor OnlineBackup.KEK k dispozici. <br>*Výchozí umístění pro pomocné složka nebo cesta k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.

## <a name="next-steps"></a>Další postup
* Získat podrobnosti o [zálohování Windows serveru s Azure Backup Agent](tutorial-backup-windows-server-to-azure.md).
* Potřebujete-li obnovit zálohu, použijte tento článek k [obnovení souborů na počítač se systémem Windows](backup-azure-restore-windows-server.md).
