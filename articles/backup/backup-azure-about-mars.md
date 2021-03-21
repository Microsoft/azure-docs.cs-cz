---
title: O agentovi MARS
description: Zjistěte, jak agent MARS podporuje scénáře zálohování.
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 8e4ace0c17dbe75e989981db56583ed9477b3716
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87562595"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>O agentovi Microsoft Azure Recovery Services (MARS)

Tento článek popisuje, jak služba Azure Backup používá agenta Microsoft Azure Recovery Services (MARS) k zálohování a obnovení souborů, složek a svazku nebo stavu systému z místního počítače do Azure.

## <a name="backup-scenarios"></a>Scénáře zálohování

Agent MARS podporuje následující scénáře zálohování:

![Scénáře zálohování MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Soubory a složky**: selektivně chránit soubory a složky systému Windows.
- **Úroveň svazku**: Chraňte celý svazek Windows počítače.
- **Úroveň systému**: ochrana celého stavu systému Windows.

### <a name="additional-scenarios"></a>Další scénáře

- **Zálohování konkrétních souborů a složek v rámci virtuálních počítačů Azure**: primární metodou pro zálohování virtuálních počítačů Azure je použití rozšíření Azure Backup na virtuálním počítači. Rozšíření zálohuje celý virtuální počítač. Pokud chcete zálohovat konkrétní soubory a složky v rámci virtuálního počítače, můžete agenta MARS nainstalovat na virtuální počítače Azure. Další informace najdete v tématu [Architektura: Integrovaná záloha virtuálních počítačů Azure](./backup-architecture.md#architecture-built-in-azure-vm-backup).

- **Offline osazení**: počáteční úplné zálohování dat do Azure obvykle přenáší velké objemy dat a vyžadují větší šířku pásma sítě. Následující zálohy přenesou jenom rozdílové nebo přírůstkové množství dat. Azure Backup zkomprimuje počáteční zálohy. Díky procesu *offline osazení* může Azure Backup použít disky k nahrání komprimovaných počátečních zálohovaných dat offline do Azure. Další informace najdete v tématu [Azure Backup offline zálohování pomocí Azure Data box](offline-backup-azure-data-box.md).

## <a name="restore-scenarios"></a>Scénáře obnovení

Agent MARS podporuje následující scénáře obnovení:

![Scénáře obnovení MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Stejný server**: Server, na kterém byla záloha původně vytvořena.
  - **Soubory a složky**: vyberte jednotlivé soubory a složky, které chcete obnovit.
  - **Úroveň svazku**: Vyberte svazek a bod obnovení, který chcete obnovit. Pak ho obnovte do stejného umístění nebo do alternativního umístění ve stejném počítači.  Vytvořte kopii existujících souborů, přepište stávající soubory nebo vynechejte obnovování stávajících souborů.
  - **Úroveň systému**: vyberte stav systému a bod obnovení, který se má obnovit do stejného počítače v zadaném umístění.

- **Alternativní server**: jiný server, než je server, na kterém se zálohování uskutečnilo.
  - **Soubory a složky**: vyberte jednotlivé soubory a složky, jejichž bod obnovení chcete obnovit do cílového počítače.
  - **Úroveň svazku**: Vyberte svazek a bod obnovení, který chcete obnovit do jiného umístění. Vytvořte kopii existujících souborů, přepište stávající soubory nebo vynechejte obnovování stávajících souborů.
  - **Úroveň systému**: vyberte stav systému a bod obnovení, který se má obnovit jako soubor stavu systému na alternativním počítači.

## <a name="backup-process"></a>Proces zálohování

1. Z Azure Portal vytvořte [trezor Recovery Services](install-mars-agent.md#create-a-recovery-services-vault)a v části **cíle zálohování** vyberte soubory, složky a stav systému.
2. [Stáhněte si přihlašovací údaje úložiště Recovery Services a instalátor agenta](./install-mars-agent.md#download-the-mars-agent) do místního počítače.

3. [Nainstalujte agenta](./install-mars-agent.md#install-and-register-the-agent) a použijte stažené přihlašovací údaje trezoru k registraci počítače do trezoru Recovery Services.
4. V konzole agenta na klientovi [nakonfigurujte zálohování](./backup-windows-with-mars-agent.md#create-a-backup-policy) tak, aby určovalo, co se má zálohovat (plán), jak dlouho se zálohy mají uchovávat v Azure (zásady uchovávání informací), a začněte chránit.

![Diagram agenta Azure Backup](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>Další informace

- **Prvotní zálohování** (první zálohování) se spouští podle nastavení zálohování.  Agent MARS používá službu Stínová kopie svazku k provedení snímků vybraných pro zálohování v čase. Agent k zachycení snímku používá pouze operaci zapisovače systému Windows. Nepoužívá žádné aplikace pro zápis služby VSS a nezachycuje snímky konzistentní vzhledem k aplikacím. Po pořízení snímku pomocí VSS vytvoří agent MARS virtuální pevný disk (VHD) ve složce mezipaměti, kterou jste zadali při konfiguraci zálohování. Agent také ukládá kontrolní součty pro každý blok dat.

- **Přírůstkové zálohování** (následné zálohy) se spouští podle plánu, který zadáte. Během přírůstkového zálohování se identifikují změněné soubory a vytvoří se nový virtuální pevný disk. Virtuální pevný disk je komprimovaný a zašifrovaný a pak se pošle do trezoru. Po dokončení přírůstkového zálohování se nový virtuální pevný disk sloučí s virtuálním pevným diskem vytvořeným po počáteční replikaci. Tento sloučený virtuální pevný disk poskytuje nejnovější stav, který se má použít pro porovnání probíhajícího zálohování.

- Agent MARS může spustit úlohu zálohování v **optimalizovaném režimu** pomocí deníku změn USN (Update Sequence Number) nebo v **neoptimalizovaném režimu** tak, že kontroluje změny v adresářích nebo souborech prostřednictvím kontroly celého svazku. Neoptimalizovaný režim je pomalejší, protože agent musí kontrolovat všechny soubory na svazku a porovnat je s metadaty, aby určil změněné soubory.  **Prvotní zálohování** se vždy spustí v neoptimalizovaném režimu. Pokud předchozí zálohování selhalo, spustí se další naplánovaná úloha zálohování v neoptimalizovaném režimu. Další informace o těchto režimech a jejich ověření najdete v [tomto článku](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode).

## <a name="next-steps"></a>Další kroky

[Matice podpory pro agenty MARS](./backup-support-matrix-mars-agent.md)

[Nejčastější dotazy k agentovi MARS](./backup-azure-file-folder-backup-faq.md)
