---
title: Povolení zálohování pro Azure Stack z portálu pro správu | Dokumentace Microsoftu
description: Povolte službu Backup infrastruktury prostřednictvím portálu pro správu služby Azure Stack je možné obnovit, pokud dojde k selhání.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: d7d47b61c926c6704a06dacc55f00d77a1266988
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51038361"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Povolení zálohování pro Azure Stack z portálu pro správu
Povolte službu Backup infrastruktury prostřednictvím portálu pro správu Azure stacku vytvářet zálohy. Tyto zálohy lze použít k obnovení svého prostředí pomocí cloudu obnovení v případě [závažnému selhání](.\azure-stack-backup-recover-data.md). Účelem zotavení cloudu je zajistit, že uživatelé a operátoři může přihlásit zpátky na portálu po dokončení obnovení. Uživatelé budou mít svá předplatná obnovit včetně oprávnění k přístupu na základě rolí a rolí, původní plány, nabídky a dříve definované výpočetní prostředky, úložiště a síťové kvóty.

Ale zálohovací služby infrastruktury zálohování virtuálních počítačů IaaS, konfigurace sítě a prostředky úložiště, jako jsou účty úložiště, objekty BLOB, tabulky, a tak dále, proto uživatelé přihlášení po obnovení cloudu dokončení nebude v některém z jejich již existující prostředky. Platforma jako služba (PaaS) prostředkům a datům také nezálohují se službou. 

Pro zálohování a obnovení prostředky IaaS a PaaS odděleně od procesů zálohování infrastruktury zodpovídají správci a uživatelé. Informace o zálohování prostředky IaaS a PaaS najdete v následujících tématech:

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Povolit nebo změnit konfiguraci zálohování

1. Otevřít [portál pro správu služby Azure Stack](azure-stack-manage-portals.md).
2. Vyberte **všechny služby**a potom v části **správu** vyberte kategorii **infrastruktura zálohování**. Zvolte **konfigurace** v **infrastruktura zálohování** okno.
3. Zadejte cestu k **umístění úložiště zálohy**. Použijte řetězec konvenci UNC (Universal Naming) pro cestu ke sdílené složce hostované na samostatném zařízení. Řetězec ve formátu UNC Určuje umístění prostředků, jako jsou sdílené soubory nebo zařízení. Pro službu můžete použít IP adresu. K zajištění dostupnosti zálohovaných dat po havárii, zařízení by měla být v samostatném umístění.

    > [!Note]  
    > Pokud vaše prostředí podporuje překlad adres v síti infrastruktura Azure stacku podnikovém prostředí, můžete použít plně kvalifikovaný název domény, nikoli IP adresu.
    
4. Typ **uživatelské jméno** použití domény a uživatelské jméno s dostatečný přístup pro čtení a zápis souborů. Například, `Contoso\backupshareuser`.
5. Typ **heslo** pro daného uživatele.
6. Zadejte heslo znovu **potvrzení hesla**.
7. **Četnost v hodinách** Určuje, jak často se vytvoří zálohy. Výchozí hodnota je 12. Scheduler podporuje maximálně 12 a minimálně 4. 
8. **Doba uchování ve dnech** Určuje, kolik dní záloh, které jsou uvedeny v externím místě. Výchozí hodnota je 7. Scheduler podporuje maximálně 14 a minimálně 2. Starší než doba uchování zálohy se automaticky odstraní z externího umístění.

    > [!Note]  
    > Pokud chcete archivovat starší než doba uchování zálohy, ujistěte se, že chcete-li zálohovat soubory předtím, než Plánovač odstraní zálohy. Pokud je zkrátit období uchovávání záloh (např. ze 7 dní na 5 dní), Plánovač odstraní všechny zálohy, které jsou starší než novým obdobím uchovávání. Ujistěte se, že máte ok se zálohami získávání odstraněn před aktualizací této hodnoty. 

9. Zadejte předsdílený klíč v **šifrovací klíč** pole. Záložní soubory jsou šifrované pomocí tohoto klíče. Ujistěte se, že tento klíč uložit na bezpečném místě. Po nastavení tohoto klíče poprvé nebo obměně klíče v budoucnu, nelze zobrazit klíč z tohoto rozhraní. Chcete-li vytvořit klíč, spusťte následující příkazy Azure Stack Powershellu:
    ```powershell
    New-AzsEncryptionKeyBase64
    ```
10. Vyberte **OK** uložte nastavení zálohování kontroleru.

    ![Azure Stack – nastavení kontroleru zálohování](media\azure-stack-backup\backup-controller-settings.png)

## <a name="start-backup"></a>Spustit zálohování
Pokud chcete spustit zálohování, klikněte na **zálohovat nyní** spustit zálohu na vyžádání. Zálohu na vyžádání nezmění čas příští plánované zálohování. Po dokončení úlohy můžete potvrdit nastavení v **Essentials**:

![Azure Stack – zálohování na vyžádání](media\azure-stack-backup\scheduled-backup.png)

Můžete taky spustit rutinu prostředí PowerShell **Start AzsBackup** ve vašem počítači pro správu služby Azure Stack. Další informace najdete v tématu [zálohování Azure stacku](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Povolí nebo zakáže automatické zálohování
Zálohy jsou automaticky naplánované povolit zálohování. Další čas plánu zálohování můžete zkontrolovat **Essentials**. 

![Azure Stack – zálohování na vyžádání](media\azure-stack-backup\on-demand-backup.png)

Pokud je nutné zakázat budoucí naplánovaných záloh, klikněte na **zakázat automatické zálohování**. Zakázat automatické zálohy budete mít nakonfigurované nastavení zálohování a zachová plán zálohování. Tato akce jednoduše dává pokyn, aby vynechat budoucí zálohy. 

![Azure Stack – zakázat naplánované zálohy](media\azure-stack-backup\disable-auto-backup.png)

Potvrďte, že budoucí naplánované zálohy byla zakázána v **Essentials**:

![Azure Stack – potvrďte zálohy jsou zakázané.](media\azure-stack-backup\confirm-disable.png)

Klikněte na **povolit automatické zálohování** informovat plánovač spustit budoucích zálohování v naplánovaném čase. 

![Azure Stack – povolení naplánované zálohy](media\azure-stack-backup\enable-auto-backup.png)


> [!Note]  
> Pokud jste nakonfigurovali zálohování infrastruktury před aktualizací na 1807, automatické zálohování se deaktivuje. Tímto způsobem zálohy tím, že Azure Stack nejsou v konfliktu s tím, že úlohu externí modul pro plánování zálohování. Když zakážete všechny externí Plánovač, klikněte na **povolit automatické zálohování**.


## <a name="next-steps"></a>Další postup

- Zjistěte, jak spustit zálohování. Zobrazit [zálohování Azure stacku](azure-stack-backup-back-up-azure-stack.md ).
- Zjistěte, jak ověřit, jestli vaše záloha spustila. Zobrazit [potvrdit zálohování bylo dokončeno v portálu pro správu](azure-stack-backup-back-up-azure-stack.md).
