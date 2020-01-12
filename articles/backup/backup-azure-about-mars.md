---
title: O agentovi MARS
description: Zjistěte, jak agent MARS podporuje scénáře zálohování.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d558a19b3025ab1400e873f97b0ce5e5a860c75a
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902861"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>O agentovi Microsoft Azure Recovery Services (MARS)

Tento článek popisuje, jak služba Azure Backup používá agenta Microsoft Azure Recovery Services (MARS) k zálohování a obnovení souborů, složek a svazku nebo stavu systému z místního počítače do Azure.

Agent MARS podporuje následující scénáře zálohování:

![Scénáře zálohování MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Soubory a složky**: selektivně chránit soubory a složky systému Windows.
- **Úroveň svazku**: Chraňte celý svazek Windows počítače.
- **Úroveň systému**: ochrana celého stavu systému Windows.

Agent MARS podporuje následující scénáře obnovení:

![Scénáře obnovení MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **Stejný server**: Server, na kterém byla záloha původně vytvořena.
    -    **Soubory a složky**: vyberte jednotlivé soubory a složky, které chcete obnovit.
    -    **Úroveň svazku**: zvolte svazek a bod obnovení, který chcete obnovit, a pak ho obnovte do stejného umístění nebo do alternativního umístění ve stejném počítači.  Vytvořte kopii existujících souborů, přepište stávající soubory nebo vynechejte obnovování stávajících souborů.
    -    **Úroveň systému**: vyberte stav systému a bod obnovení, který se má obnovit do stejného počítače v zadaném umístění.


-   **Alternativní server**: jiný server, než je server, na kterém se zálohování uskutečnilo.
    -    **Soubory a složky**: vyberte jednotlivé soubory a složky, jejichž bod obnovení chcete obnovit do cílového počítače.
    -    **Úroveň svazku**: Vyberte svazek a bod obnovení, který chcete obnovit do jiného umístění. Vytvořte kopii existujících souborů, přepište stávající soubory nebo vynechejte obnovování stávajících souborů.
    -    **Úroveň systému**: vyberte stav systému a bod obnovení, který se má obnovit jako soubor stavu systému na alternativním počítači.

## <a name="backup-process"></a>Proces zálohování

1. Z Azure Portal vytvořte [trezor Recovery Services](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault)a v části cíle zálohování vyberte soubory, složky a stav systému.
2. [Stáhněte si přihlašovací údaje úložiště Recovery Services a instalátor agenta](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) do místního počítače. 

    Chcete-li chránit místní počítač, vyberte možnost zálohování, zvolte možnost soubory, složky a stav systému a poté Stáhněte agenta MARS.

3. Příprava infrastruktury:

    a. Spusťte instalační program a [nainstalujte agenta](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent).

    b. K registraci počítače do trezoru Recovery Services použijte své stažené přihlašovací údaje trezoru.
4. V konzole agenta na klientovi [nakonfigurujte zálohu](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy). Zadejte zásady uchovávání dat záloh, aby se začaly chránit.

![Diagram agenta Azure Backup](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>Další scénáře
-   **Zálohování konkrétních souborů a složek v rámci virtuálních počítačů Azure**: primární metodou pro zálohování virtuálních počítačů Azure je použití rozšíření Azure Backup na virtuálním počítači. Rozšíření zálohuje celý virtuální počítač. Pokud chcete zálohovat konkrétní soubory a složky v rámci virtuálního počítače, můžete agenta MARS nainstalovat na virtuální počítače Azure. Další informace najdete v tématu [Architektura: Integrovaná záloha virtuálních počítačů Azure](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

-   **Offline osazení**: počáteční úplné zálohování dat do Azure obvykle přenáší velké objemy dat a vyžadují větší šířku pásma sítě. Následující zálohy přenesou jenom rozdílové nebo přírůstkové množství dat. Azure Backup zkomprimuje počáteční zálohy. Díky procesu *offline osazení*může Azure Backup použít disky k nahrání komprimovaných počátečních zálohovaných dat offline do Azure. Další informace najdete v tématu [pracovní postup offline zálohování pro aplikaci DPM a Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).


## <a name="next-steps"></a>Další kroky
[Matice podpory agenta MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Nejčastější dotazy k agentovi MARS](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
