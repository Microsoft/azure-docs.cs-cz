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
ms.date: 05/11/2018
ms.author: jeffgilb
ms.openlocfilehash: 08bce6284b672ae092e2cee3c26140e8c6049a34
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242848"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Povolení zálohování pro Azure Stack z portálu pro správu
Povolte službu Backup infrastruktury prostřednictvím portálu pro správu Azure stacku vytvářet zálohy. Tyto zálohy lze použít k obnovení svého prostředí pomocí cloudu obnovení v případě [závažnému selhání](.\azure-stack-backup-recover-data.md). Účelem zotavení cloudu je zajistit, že uživatelé a operátoři může přihlásit zpátky na portálu po dokončení obnovení. Uživatelé budou mít svá předplatná obnovit včetně oprávnění k přístupu na základě rolí a rolí, původní plány, nabídky a dříve definované výpočetní prostředky, úložiště a síťové kvóty.

Ale zálohovací služby infrastruktury zálohování virtuálních počítačů IaaS, konfigurace sítě a prostředky úložiště, jako jsou účty úložiště, objekty BLOB, tabulky, a tak dále, proto uživatelé přihlášení po obnovení cloudu dokončení nebude v některém z jejich již existující prostředky. Platforma jako služba (PaaS) prostředkům a datům také nezálohují se službou. 

Pro zálohování a obnovení prostředky IaaS a PaaS odděleně od procesů zálohování infrastruktury zodpovídají správci a uživatelé. Informace o zálohování prostředky IaaS a PaaS najdete v následujících tématech:

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> Před povolením zálohování prostřednictvím konzole, musíte nakonfigurovat zálohovací služby. Můžete nakonfigurovat službu backup pomocí Powershellu. Další informace najdete v tématu [povolit zálohování pro Azure Stack s prostředím PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Povolit zálohování

1. Otevřít na portálu pro správu služby Azure Stack na [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Vyberte **další služby** > **infrastruktura zálohování**. Zvolte **konfigurace** v **infrastruktura zálohování** okno.

    ![Azure Stack – nastavení kontroleru zálohování](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Zadejte cestu k **umístění úložiště zálohy**. Použijte řetězec konvenci UNC (Universal Naming) pro cestu ke sdílené složce hostované na samostatném zařízení. Řetězec ve formátu UNC Určuje umístění prostředků, jako jsou sdílené soubory nebo zařízení. Pro službu můžete použít IP adresu. K zajištění dostupnosti zálohovaných dat po havárii, zařízení by měla být v samostatném umístění.
    > [!Note]  
    > Pokud vaše prostředí podporuje překlad adres v síti infrastruktura Azure stacku podnikovém prostředí, můžete použít plně kvalifikovaný název domény, nikoli IP adresu.
4. Typ **uživatelské jméno** použití domény a uživatelské jméno s dostatečný přístup pro čtení a zápis souborů. Například, `Contoso\backupshareuser`.
5. Typ **heslo** pro daného uživatele.
5. Zadejte heslo znovu **potvrzení hesla**.
6. Zadejte předsdílený klíč v **šifrovací klíč** pole. Záložní soubory jsou šifrované pomocí tohoto klíče. Ujistěte se, že tento klíč uložit na bezpečném místě. Po nastavení tohoto klíče poprvé nebo v budoucnu obměně klíče za nemůže zobrazit tento klíč z tohoto rozhraní. Další pokyny ke generování předsdíleným klíčem, postupujte podle skripty na [povolit zálohování pro Azure Stack s prostředím PowerShell](azure-stack-backup-enable-backup-powershell.md).
7. Vyberte **OK** uložte nastavení zálohování kontroleru.

K provedení zálohování, budete muset stáhnout nástroje Azure Stack a pak spusťte rutinu prostředí PowerShell **Start AzSBackup** uzlu Správa služby Azure Stack. Další informace najdete v tématu [zálohování Azure stacku](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Další postup

- Zjistěte, jak spustit zálohování. Zobrazit [zálohování Azure stacku](azure-stack-backup-back-up-azure-stack.md ).
- Zjistěte, jak ověřit, jestli vaše záloha spustila. Zobrazit [potvrdit zálohování bylo dokončeno v portálu pro správu](azure-stack-backup-back-up-azure-stack.md).