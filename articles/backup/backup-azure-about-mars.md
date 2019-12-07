---
title: O agentovi MARS
description: Zjistěte, jak agent MARS podporuje scénáře zálohování.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c036d93c09195c0c330cfe86f307d28866131d9f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897322"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>O agentovi Microsoft Azure Recovery Services (MARS)

Tento článek popisuje, jak služba Azure Backup používá agenta Microsoft Azure Recovery Services (MARS) k zálohování a obnovení souborů či složek, svazku nebo stavu systému z místního počítače do Azure.

Agent MARS podporuje následující scénáře zálohování:

![řídicí panel trezoru služby Recovery Services](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Soubory a složky**: selektivně chránit soubory a složky systému Windows.
- **Úroveň svazku**: Chraňte celý svazek Windows počítače.
- **Úroveň systému**: ochrana celého stavu systému Windows.

Agent MARS podporuje následující scénáře obnovení:

![řídicí panel trezoru služby Recovery Services](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **Stejný server**: stejný server, na kterém se původně vytvořila záloha.
    -    **Soubory a složky**: můžete procházet a zvolit jednotlivé soubory a složky, které chcete obnovit.
    -    **Úroveň svazku**: můžete vybrat svazek a bod obnovení, který chcete obnovit, a obnovit ho do stejného umístění nebo alternativního umístění ve stejném počítači.  Můžete buď vytvořit kopii existujících souborů, přepsat existující soubory nebo přeskočit obnovování stávajících souborů.
    -    **Úroveň systému**: můžete zvolit stav systému a bod obnovení, který se má obnovit do stejného počítače v zadaném umístění.


-   **Alternativní server**: jiný server, to znamená, že se nejedná o stejný server, na kterém byla záloha provedena.
    -    **Soubory a složky**: můžete procházet a zvolit jednotlivé soubory a složky, které chcete obnovit v cílovém počítači.
    -    **Úroveň svazku**: můžete vybrat svazek a bod obnovení, který chcete obnovit do alternativního umístění, a to tak, že vytvoříte kopii existujících souborů, přepíšete existující soubory nebo vynecháte obnovení existujících souborů.
    -    **Úroveň systému**: můžete zvolit stav systému a bod obnovení, který se má obnovit jako soubor stavu systému na alternativním počítači.

## <a name="backup-process"></a>Proces zálohování

1.  Z Azure Portal vytvořte [trezor služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault) a v části cíle zálohování vyberte soubory a složky a stav systému.
2.  [Stáhněte](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) si přihlašovací údaje trezoru služby Recovery Services a instalátor agenta do místního počítače. Chcete-li chránit místní počítač, vyberte možnost zálohování a pak zvolte soubory a složky a stav systému a stáhněte agenta MARS.
3.  Příprava infrastruktury:

    a.    Spusťte instalační program a [nainstalujte](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) agenta.

    b.  Použijte stažené přihlašovací údaje trezoru k registraci počítače do úložiště Recovery Services.
4.  V konzole agenta na klientovi pomocí nástroje [naplánovat zálohování](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy) nakonfigurujte zálohování. Zadejte zásady uchovávání dat záloh a začněte chránit.

![řídicí panel trezoru služby Recovery Services](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>Další scénáře
-   **Zálohování specifických souborů a složek ve virtuálním počítači Azure** – primární metodou pro zálohování virtuálních počítačů Azure je použití rozšíření Azure Backup na virtuálním počítači. Tím se zálohuje celý virtuální počítač. Pokud chcete zálohovat konkrétní soubory a složky na virtuálním počítači, můžete agenta MARS nainstalovat na virtuální počítače Azure. [Další informace](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

-   **Offline osazení** – počáteční úplné zálohování dat do Azure, obvykle přenáší velké objemy dat a vyžaduje větší šířku pásma v porovnání s dalšími zálohami, které přenášejí jenom rozdílová/přírůstková. Azure Backup zkomprimuje počáteční zálohy. Díky procesu offline osazení může Azure Backup použít disky k nahrání komprimovaných počátečních zálohovaných dat offline do Azure. [Další informace](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).


## <a name="next-steps"></a>Další kroky
[Matice podpory agenta MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Nejčastější dotazy – agent MARS](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
