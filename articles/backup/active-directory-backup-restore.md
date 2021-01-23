---
title: Zálohování a obnovení služby Active Directory
description: Naučte se zálohovat a obnovovat řadiče domény služby Active Directory.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8db2dab605e90e4748b11a632d6651c23d631b6c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733549"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Zálohování a obnovení řadičů domény služby Active Directory

Zálohování služby Active Directory a zajištění úspěšných obnovení v případech poškození, ohrožení bezpečnosti nebo havárie je důležitou součástí údržby služby Active Directory.

Tento článek popisuje správné postupy pro zálohování a obnovení řadičů domény služby Active Directory pomocí Azure Backup, ať už jde o virtuální počítače Azure nebo místní servery. Popisuje situaci, kdy potřebujete obnovit celý řadič domény do stavu v době zálohování. Pokud chcete zjistit, který scénář obnovení je pro vás vhodný, přečtěte si [Tento článek](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover).  

>[!NOTE]
> Tento článek se nezabývá obnovením položek z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Informace o obnovení Azure Active Directory uživatelů najdete v [tomto článku](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="best-practices"></a>Osvědčené postupy

- Ujistěte se, že je alespoň jeden řadič domény zálohovaný. Pokud zálohujete více než jeden řadič domény, ujistěte se, že jsou zálohované všechny [role FSMO (Flexible Single Master Operation)](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) .

- Pravidelně zálohujte službu Active Directory. Záloha by nikdy neměla být větší než doba nesprávného stavu (ve výchozím nastavení je to 60 dní), protože objekty starší než doba platnosti za neplatný budou označeny jako "neplatné" a již nejsou považovány za platné.

- Máte jasný plán zotavení po havárii, který obsahuje pokyny k obnovení řadičů domény. Pokud chcete připravit obnovení doménové struktury služby Active Directory, přečtěte si [příručku pro obnovení doménové struktury služby Active Directory](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide).

- Pokud potřebujete obnovit řadič domény a nechat v doméně zbývající funkční řadič domény, můžete místo obnovení ze zálohy vytvořit nový server. Přidejte roli serveru **Active Directory Domain Services** k novému serveru a nastavte ho jako řadič domény v existující doméně. Data služby Active Directory se pak replikují na nový server. Chcete-li odstranit předchozí řadič domény ze služby Active Directory, postupujte podle kroků [v tomto článku](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) a proveďte vyčištění metadat.

>[!NOTE]
>Azure Backup nezahrnuje obnovení na úrovni položek pro službu Active Directory. Pokud chcete obnovit odstraněné objekty a máte přístup k řadiči domény, použijte [Koš služby Active Directory](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt). Pokud tato metoda není k dispozici, můžete použít zálohování řadiče domény k obnovení odstraněných objektů pomocí nástroje pro **ntdsutil.exe** , jak je popsáno [zde](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac).
>
>Informace o tom, jak provést autoritativní obnovení adresáře SYSVOL, najdete v [tomto článku](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol).

## <a name="backing-up-azure-vm-domain-controllers"></a>Zálohování řadičů domény virtuálních počítačů Azure

Pokud je řadič domény virtuálním počítačem Azure, můžete server zálohovat pomocí [zálohování virtuálních počítačů Azure](backup-azure-vms-introduction.md).

Přečtěte si informace o [provozních faktorech virtualizovaného řadiče domény](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers) , abyste zajistili úspěšné zálohování (a budoucí obnovení) řadičů domény virtuálních počítačů Azure.

## <a name="backing-up-on-premises-domain-controllers"></a>Zálohování místních řadičů domény

Pokud chcete zálohovat místní řadič domény, musíte zálohovat data stavu systému serveru.

- Pokud používáte MARS, postupujte podle [těchto pokynů](backup-azure-system-state.md).
- Pokud používáte MABS (Azure Backup Server), postupujte podle [těchto pokynů](backup-mabs-system-state-and-bmr.md).

>[!NOTE]
> Obnovení místních řadičů domény (buď ze stavu systému nebo z virtuálních počítačů) do cloudu Azure se nepodporuje. Pokud chcete mít možnost převzetí služeb při selhání z místního prostředí Active Directory do Azure, zvažte použití [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md).

## <a name="restoring-active-directory"></a>Obnovení služby Active Directory

Data služby Active Directory je možné obnovit v jednom ze dvou režimů: **autoritativní** nebo **neautoritativní**. V autoritativním obnovení budou obnovená data služby Active Directory přepsat data zjištěná na ostatních řadičích domény v doménové struktuře.

V tomto scénáři se ale nově sestavuje řadič domény v existující doméně, takže by se mělo provést **neautoritativní** obnovení.

Během obnovování se Server spustí v režimu obnovení adresářových služeb (DSRM). Pro režim obnovení adresářových služeb budete muset zadat heslo správce.

>[!NOTE]
>Pokud je zapomenuté heslo DSRM, můžete ho resetovat podle [těchto pokynů](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11)).

### <a name="restoring-azure-vm-domain-controllers"></a>Obnovování řadičů domény virtuálních počítačů Azure

Pokud chcete obnovit řadič domény virtuálního počítače Azure, přečtěte si téma [obnovení virtuálních počítačů řadiče domény](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).

Pokud obnovujete jeden virtuální počítač řadiče domény nebo několik virtuálních počítačů řadiče domény v jedné doméně, obnovte je jako jakýkoli jiný virtuální počítač. K dispozici je také režim obnovení adresářových služeb (DSRM), aby všechny scénáře obnovení služby Active Directory byly životaschopné.

Pokud potřebujete obnovit jeden virtuální počítač řadiče domény v konfiguraci s více doménami, obnovte disky a vytvořte virtuální počítač [pomocí PowerShellu](backup-azure-vms-automation.md#restore-the-disks).

Pokud obnovujete poslední zbývající řadič domény v doméně nebo obnovujete více domén v jedné doménové struktuře, doporučujeme [obnovení doménové struktury](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).

>[!NOTE]
> Virtualizované řadiče domény, od verze Windows 2012 a vyšší používají [ochranu na základě virtualizace](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards). Díky těmto ochranám služba Active Directory rozumí, jestli je obnovený virtuální počítač řadičem domény, a provede nezbytné kroky k obnovení dat služby Active Directory.

### <a name="restoring-on-premises-domain-controllers"></a>Obnovování místních řadičů domény

Pokud chcete obnovit místní řadič domény, postupujte podle pokynů v části pro obnovení stavu systému Windows Server s využitím pokynů pro [obnovení stavu systému v řadiči domény](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller).

## <a name="next-steps"></a>Další kroky

- [Matice podpory pro Azure Backup](backup-support-matrix.md)