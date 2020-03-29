---
title: O agentovi MARS
description: Zjistěte, jak agent MARS podporuje scénáře zálohování
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673294"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>O agentovi služby Microsoft Azure Recovery Services (MARS)

Tento článek popisuje, jak služba Azure Backup používá agenta Microsoft Azure Recovery Services (MARS) k zálohování a obnovení souborů, složek a stavu svazku nebo systému z místního počítače do Azure.

Agent MARS podporuje následující scénáře zálohování:

![Scénáře zálohování MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Soubory a složky**: Selektivně chránit soubory a složky systému Windows.
- **Úroveň hlasitosti**: Chraňte celý svazek systému Windows vašeho počítače.
- **Úroveň systému**: Chraňte celý stav systému Windows.

Agent MARS podporuje následující scénáře obnovení:

![Scénáře obnovení MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Stejný server**: Server, na kterém byla záloha původně vytvořena.
  - **Soubory a složky**: Zvolte jednotlivé soubory a složky, které chcete obnovit.
  - **Úroveň hlasitosti**: Zvolte svazek a bod obnovení, který chcete obnovit, a pak jej obnovte do stejného nebo alternativního umístění ve stejném počítači.  Vytvořte kopii existujících souborů, přepište existující soubory nebo přeskočte obnovení existujících souborů.
  - **Úroveň systému**: Zvolte stav systému a bod obnovení, který chcete obnovit do stejného počítače v určeném umístění.

- **Alternativní server**: Jiný server než server, na kterém byla záloha odebrána.
  - **Soubory a složky**: Zvolte jednotlivé soubory a složky, jejichž bod obnovení chcete obnovit do cílového počítače.
  - **Úroveň hlasitosti**: Zvolte svazek a bod obnovení, který chcete obnovit do jiného umístění. Vytvořte kopii existujících souborů, přepište existující soubory nebo přeskočte obnovení existujících souborů.
  - **Úroveň systému**: Zvolte stav systému a bod obnovení, který chcete obnovit jako soubor Stavu systému do alternativního počítače.

## <a name="backup-process"></a>Proces zálohování

1. Na webu Azure Portal vytvořte [trezor služby Recovery Services](install-mars-agent.md#create-a-recovery-services-vault)a z cílů zálohování zvolte soubory, složky a stav systému.
2. [Stáhněte přihlašovací údaje trezoru služby Recovery Services a instalačníprogram agenta](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) do místního počítače.

    Chcete-li chránit místní počítač výběrem možnosti Zálohování, zvolte soubory, složky a stav systému a stáhněte agenta MARS.

3. Příprava infrastruktury:

    a. Spusťte instalační program a [nainstalujte agenta](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent).

    b. Pomocí stažených přihlašovacích údajů trezoru zaregistrujte počítač do trezoru služby Recovery Services.
4. Z konzoly agenta na straně [klienta nakonfigurujte zálohu](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy). Zadejte zásady uchovávání zálohovacích dat, abyste je začali chránit.

![Diagram agenta azure backup](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>Další scénáře

- **Zálohování konkrétních souborů a složek v rámci virtuálních počítačů Azure:** Primární metodou zálohování virtuálních počítačů Azure (VM) je použití rozšíření Azure Backup na virtuálním počítači. Rozšíření zálohy celý virtuální ho. Pokud chcete zálohovat konkrétní soubory a složky v rámci virtuálního počítače, můžete nainstalovat agenta MARS ve virtuálních počítačích Azure. Další informace najdete [v tématu Architektura: Integrovaná záloha virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

- **Offline osevací**: Počáteční úplné zálohování dat do Azure obvykle přenášet velké množství dat a vyžadují větší šířku pásma sítě. Následné zálohy přenášejí pouze rozdílové nebo přírůstkové množství dat. Azure Backup komprimuje počáteční zálohy. Prostřednictvím procesu *offline osevní*, Azure Backup můžete použít disky k nahrání komprimované počáteční záložní data offline do Azure. Další informace najdete v tématu [Azure Backup offline zálohování pomocí Azure Data Box](offline-backup-azure-data-box.md).

## <a name="next-steps"></a>Další kroky

[Matice podpory pro agenty MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Nejčastější dotazy agenta MARS](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
