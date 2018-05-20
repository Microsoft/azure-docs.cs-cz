---
title: Povolit zálohování pro zásobník Azure z portálu pro správu | Microsoft Docs
description: Povolte službu infrastruktura zálohování prostřednictvím portálu pro správu, aby zásobník Azure můžete obnovit, pokud dojde k selhání.
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
ms.date: 05/11/2018
ms.author: jeffgilb
ms.openlocfilehash: ce5fd2feaa30948042cc0570a4b0ea7f0ab7ad77
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Povolit zálohování pro zásobník Azure z portálu pro správu
Povolte službu infrastruktura zálohování prostřednictvím portálu pro správu tak, aby zásobník Azure může generovat zálohy. Tyto zálohy můžete použít k obnovení vašeho prostředí pomocí cloudu obnovení v případě [k závažnému selhání](.\azure-stack-backup-recover-data.md). Účelem obnovení cloudu je zajistit, že operátory a uživatele můžete znovu se přihlásili k portálu po dokončení obnovení. Uživatelé budou mít předplatné obnovit včetně oprávnění k přístupu na základě rolí a rolí, původní plány, nabízí a předem definované výpočetní, úložiště a síťové kvóty.

Ale infrastruktury služby zálohování nejsou zálohování virtuálních počítačů IaaS, konfigurace sítě a prostředky úložiště, jako je například účty úložiště objektů BLOB, tabulek a tak dále, tak uživatelům přihlášení po obnovení cloudu dokončí, se nezobrazí žádné z dříve stávající prostředky. Platforma jako služba (PaaS) prostředky a data nejsou také zálohovat pomocí služby. 

Správci a uživatelé jsou zodpovědní za zálohování a obnovení prostředky IaaS a PaaS odděleně od procesů zálohování infrastruktury. Informace o zálohování prostředky IaaS a PaaS v následujících tématech:

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> Než povolíte zálohování prostřednictvím konzoly, musíte nakonfigurovat službu zálohování. Můžete nakonfigurovat pomocí prostředí PowerShell služby zálohování. Další informace najdete v tématu [povolit zálohování pro zásobník Azure pomocí prostředí PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Povolení zálohování

1. Otevřete na portálu správy Azure zásobníku na [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Vyberte **další služby** > **infrastruktura zálohování**. Zvolte **konfigurace** v **infrastruktura zálohování** okno.

    ![Azure zásobníku – nastavení zálohování řadiče](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Zadejte cestu ke **umístění úložiště zálohy**. Pomocí řetězce Universal Naming Convention (UNC) pro cestu ke sdílené složce hostované na samostatných zařízení. Řetězec UNC Určuje umístění prostředků, jako jsou sdílené soubory nebo zařízení. Pro službu můžete použít IP adresu. K zajištění dostupnosti zálohovaných dat po havárii, zařízení by měl být v samostatné umístění.
    > [!Note]  
    > Pokud vaše prostředí podporuje překlad adres ze sítě infrastruktury Azure zásobníku podnikovém prostředí, můžete použít plně kvalifikovaný název domény, nikoli IP adresu.
4. Typ **uživatelské jméno** použití domény a uživatelské jméno s dostatečný přístup pro čtení a zápis souborů. Například, `Contoso\backupshareuser`.
5. Typ **heslo** pro uživatele.
5. Zadejte heslo znovu **Potvrdit heslo**.
6. Zadat předsdílený klíč v **šifrovací klíč** pole. Záložní soubory jsou šifrované pomocí tohoto klíče. Ujistěte se, že tento klíč uložit na bezpečném místě. Jakmile poprvé nastavit tento klíč nebo klíč Otočit v budoucnu, nelze zobrazit, tento klíč z tohoto rozhraní. Další pokyny ke generování předsdílený klíč, postupujte podle skripty v [povolit zálohování pro zásobník Azure pomocí prostředí PowerShell](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key). 
7. Vyberte **OK** uložte nastavení zálohování řadiče.

K provedení zálohy, musíte ke stažení nástroje Azure zásobníku a poté spusťte rutinu prostředí PowerShell **Start-AzSBackup** vašeho uzlu Azure zásobníku správy. Další informace najdete v tématu [zálohování Azure zásobníku](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Další postup

- Naučte se spustit zálohování. V tématu [zálohování Azure zásobníku](azure-stack-backup-back-up-azure-stack.md ).
- Naučte se ověřit, jestli vaše zálohování spustila. V tématu [zálohování potvrzení byla dokončena v portálu pro správu](azure-stack-backup-back-up-azure-stack.md).
