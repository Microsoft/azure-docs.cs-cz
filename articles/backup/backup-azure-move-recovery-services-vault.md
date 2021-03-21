---
title: Jak přesunout trezory služby Azure Backup Recovery Services
description: Pokyny, jak přesunout Recovery Services trezor mezi předplatnými Azure a skupinami prostředků.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 4f75bec533181b29625fb0a10cc26d03f2875036
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466367"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Přesunutí trezoru Recovery Services napříč předplatnými Azure a skupinami prostředků

Tento článek vysvětluje, jak přesunout trezor Recovery Services nakonfigurovaný pro Azure Backup v rámci předplatných Azure nebo do jiné skupiny prostředků ve stejném předplatném. K přesunutí trezoru Recovery Services můžete použít Azure Portal nebo PowerShell.

## <a name="supported-regions"></a>Podporované oblasti

Podporují se všechny veřejné regiony a oblasti svrchovaného centra, s výjimkou Francie – střed, Francie – jih, Německo – severovýchod, Německo – střed, Čína – sever, Čína North2, Čína – východ a Čína – východ 2.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Předpoklady pro přesunutí trezoru Recovery Services

- Při přesunu trezoru mezi skupinami prostředků jsou zdrojové i cílové skupiny prostředků zamčené, aby se zabránilo operacím zápisu a odstranění. Další informace najdete v tomto [článku](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Pouze předplatné správce má oprávnění k přesunu trezoru.
- Pro přesun trezorů mezi předplatnými se musí cílové předplatné nacházet ve stejném tenantovi jako zdrojové předplatné a jeho stav musí být povolený. Pokud chcete přesunout trezor do jiného adresáře služby Azure AD, přečtěte si článek [přenos předplatného do jiného adresáře](../role-based-access-control/transfer-subscription.md) a [Nejčastější dotazy k trezoru služby Recovery Services](backup-azure-backup-faq.md#recovery-services-vault).
- Musíte mít oprávnění k provádění operací zápisu v cílové skupině prostředků.
- Přesun trezoru mění jenom skupinu prostředků. Trezor Recovery Services se bude nacházet ve stejném umístění a nedá se změnit.
- V jednom okamžiku můžete přesunout jenom jeden Recovery Services trezor pro oblast.
- Pokud se virtuální počítač nepřesouvá s Recovery Servicesovým trezorem v rámci předplatných nebo na novou skupinu prostředků, v trezoru zůstanou v trezoru nedotčené, dokud nevyprší jejich platnost.
- Bez ohledu na to, jestli se virtuální počítač přesune do trezoru, nebo ne, můžete virtuální počítač vždycky obnovit z historie uchovávané zálohy v trezoru.
- Azure Disk Encryption vyžaduje, aby se Trezor klíčů a virtuální počítače nacházely ve stejné oblasti a předplatném Azure.
- Postup přesunutí virtuálního počítače se spravovanými disky najdete v tomto [článku](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Možnosti přesunutí prostředků nasazených přes klasický model se liší v závislosti na tom, zda přesouváte prostředky v rámci předplatného nebo do nového předplatného. Další informace najdete v tomto [článku](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Zásady zálohování definované pro trezor se uchovávají po přesunu trezoru mezi předplatnými nebo do nové skupiny prostředků.
- Můžete přesunout jenom trezor, který obsahuje některý z následujících typů zálohových položek. Všechny zálohované položky typů, které nejsou uvedené níže, se musí zastavit a data se před přesunutím trezoru trvale odstraní.
  - Azure Virtual Machines
  - Agent Microsoft Azure Recovery Services (MARS)
  - Server Microsoft Azure Backup (MABS)
  - Data Protection Manager (DPM)
- Pokud přesunete trezor obsahující data záloh virtuálních počítačů v rámci předplatných, musíte virtuální počítače přesunout do stejného předplatného, a pokud chcete pokračovat v zálohování, použijte stejný název cílové skupiny prostředků virtuálního počítače (stejně jako ve starém předplatném).

> [!NOTE]
> Přesunutí Recovery Services trezorů pro Azure Backup napříč oblastmi Azure se nepodporuje.<br><br>
> Pokud jste nakonfigurovali nějaké virtuální počítače (Azure IaaS, Hyper-V, VMware) nebo fyzické počítače pro zotavení po havárii pomocí **Azure Site Recovery**, operace přesunu se zablokuje. Pokud chcete přesunout trezory pro Azure Site Recovery, přečtěte si [Tento článek](../site-recovery/move-vaults-across-regions.md) , kde najdete informace o ručních přesunech trezorů.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Použití Azure Portal k přesunu Recovery Services trezoru do jiné skupiny prostředků

Přesunutí trezoru Recovery Services a přidružených prostředků do jiné skupiny prostředků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Otevřete seznam **trezorů Recovery Services** a vyberte trezor, který chcete přesunout. Po otevření řídicího panelu trezoru se zobrazí, jak je znázorněno na následujícím obrázku.

   ![Otevřít Recovery Services trezor](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Pokud nevidíte **základní** informace pro váš trezor, vyberte ikonu rozevíracího seznamu. Teď byste měli vidět základní informace o vašem trezoru.

   ![Karta informace o Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. V nabídce přehled trezoru vyberte **změnit** vedle **skupiny prostředků** a otevřete tak podokno **přesunout prostředky** .

   ![Změnit skupinu prostředků](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. V podokně **přesunout prostředky** pro vybraný trezor doporučujeme přesunout volitelné související prostředky tak, že vyberete zaškrtávací políčko, jak je znázorněno na následujícím obrázku.

   ![Přesunout předplatné](./media/backup-azure-move-recovery-services/move-resource.png)

5. Pokud chcete přidat cílovou skupinu prostředků, v rozevíracím seznamu **Skupina prostředků** vyberte existující skupinu prostředků nebo vyberte možnost **vytvořit novou skupinu** .

   ![Vytvořit prostředek](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Po přidání skupiny prostředků potvrďte **, že nástroje a skripty přidružené k přesunutým prostředkům nebudou fungovat, dokud je neaktualizujete tak, aby používaly nové ID prostředků** , a pak kliknutím na **OK** dokončete přesun trezoru.

   ![Potvrzovací zpráva](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Použití Azure Portal k přesunu Recovery Services trezoru do jiného předplatného

Můžete přesunout Recovery Services trezor a jeho přidružené prostředky do jiného předplatného.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Otevřete seznam trezorů Recovery Services a vyberte trezor, který chcete přesunout. Po otevření řídicího panelu trezoru se zobrazí, jak je znázorněno na následujícím obrázku.

    ![Otevřít Recovery Services trezor](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Pokud nevidíte **základní** informace pro váš trezor, vyberte ikonu rozevíracího seznamu. Teď byste měli vidět základní informace o vašem trezoru.

    ![Karta informace o Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. V nabídce přehled trezoru vyberte **změnit** u možností **předplatné** a otevřete podokno **přesunout prostředky** .

   ![Změnit předplatné](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Vyberte prostředky, které se mají přesunout. tady doporučujeme, abyste pro výběr všech uvedených volitelných prostředků použili možnost **Vybrat vše** .

   ![přesunout prostředek](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. V rozevíracím seznamu **odběr** vyberte cílové předplatné, kde chcete trezor přesunout.
6. Pokud chcete přidat cílovou skupinu prostředků, v rozevíracím seznamu **Skupina prostředků** vyberte existující skupinu prostředků nebo vyberte možnost **vytvořit novou skupinu** .

   ![Přidat předplatné](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Vyberte **, že nástroje a skripty přidružené k přesunutým prostředkům nebudou fungovat, dokud je neaktualizujete tak, aby pro potvrzení používaly nové identifikátory prostředků** , a pak vyberte **OK**.

> [!NOTE]
> Zálohování mezi předplatnými (trezor RS a chráněné virtuální počítače jsou v různých předplatných) není podporovaný scénář. Možnost redundance úložiště z místního redundantního úložiště (LRS) do globálního redundantního úložiště (GRS) a naopak se nedá změnit během operace přesunu trezoru.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Použití PowerShellu k přesunutí trezoru Recovery Services

Pokud chcete přesunout Recovery Services trezor do jiné skupiny prostředků, použijte `Move-AzureRMResource` rutinu. `Move-AzureRMResource` vyžaduje název prostředku a typ prostředku. Z rutiny můžete získat obojí `Get-AzureRmRecoveryServicesVault` .

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Chcete-li přesunout prostředky do jiného předplatného, zahrňte `-DestinationSubscriptionId` parametr.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Po provedení výše uvedených rutin budete požádáni o potvrzení, že chcete přesunout zadané prostředky. Zadáním **Y** potvrďte. Po úspěšném ověření se prostředek přesune.

## <a name="use-cli-to-move-recovery-services-vault"></a>Přesunutí Recovery Services trezoru pomocí rozhraní příkazového řádku

Pokud chcete přesunout Recovery Services trezor do jiné skupiny prostředků, použijte tuto rutinu:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Pokud chcete přejít k novému předplatnému, zadejte `--destination-subscription-id` parametr.

## <a name="post-migration"></a>Po dokončení migrace

1. Nastaví nebo ověří řízení přístupu pro skupiny prostředků.  
2. Po dokončení přesunu je třeba pro trezor znovu nakonfigurovat funkci vytváření sestav a monitorování zálohování. Předchozí konfigurace bude ztracena během operace přesunutí.

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Přesuňte virtuální počítač Azure do jiného trezoru služby Recovery Services. 

Pokud chcete přesunout virtuální počítač Azure s povoleným zálohováním Azure, máte dvě možnosti. Závisí na vašich obchodních požadavcích:

- [Nemusíte uchovávat předchozí zálohovaná data.](#dont-need-to-preserve-previous-backed-up-data)
- [Musí zachovat předchozí zálohovaná data.](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Nemusíte uchovávat předchozí zálohovaná data.

Aby bylo možné chránit úlohy v novém trezoru, bude nutné odstranit aktuální ochranu a data ve starém trezoru a znovu nakonfigurovat zálohování.

>[!WARNING]
>Následující operace je destruktivní a nelze ji vrátit zpět. Všechna zálohovaná data a zálohované položky přidružené k chráněnému serveru budou trvale odstraněny. Postupujte však opatrně.

**Zastavte a odstraňte aktuální ochranu ve starém trezoru:**

1. Ve vlastnostech trezoru zakažte obnovitelné odstranění. Chcete-li zakázat obnovitelné odstranění, postupujte podle [těchto kroků](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) .

2. Zastavte ochranu a odstraňte zálohy z aktuálního trezoru. V nabídce řídicího panelu trezoru vyberte **zálohované položky**. Zde uvedené položky, které je třeba přesunout do nového trezoru, je třeba odebrat spolu s jejich zálohovanými daty. Podívejte se, jak [Odstranit chráněné položky v cloudu](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) a [Odstranit chráněné položky místně](backup-azure-delete-vault.md#delete-protected-items-on-premises).

3. Pokud plánujete přesunout službu AFS (sdílené složky Azure), servery SQL nebo SAP HANA servery, budete je muset taky zrušit. V nabídce řídicího panelu trezoru vyberte **infrastruktura zálohování**. Podívejte se, jak [zrušit registraci SQL serveru](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance), [zrušit registraci účtu úložiště přidruženého ke sdíleným složkám Azure](manage-afs-backup.md#unregister-a-storage-account)a [zrušit registraci instance SAP HANA](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

4. Až budou odebrané ze starého trezoru, pokračujte v konfiguraci záloh pro vaše úlohy v novém trezoru.

### <a name="must-preserve-previous-backed-up-data"></a>Musí zachovat předchozí zálohovaná data.

Pokud potřebujete zachovat aktuální chráněná data ve starém trezoru a pokračovat v ochraně v novém trezoru, existují pro některé úlohy omezené možnosti:

- U služby MARS můžete [Zastavit ochranu a zachovávat data](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) a zaregistrovat agenta v novém trezoru.

  - Služba Azure Backup bude nadále uchovávat všechny existující body obnovení starého trezoru.
  - Abyste zachovali body obnovení ve starém trezoru, budete muset platit.
  - Zálohovaná data bude možné obnovit pouze v případě neplatných bodů obnovení ve starém trezoru.
  - V novém trezoru se musí vytvořit nová počáteční replika dat.

- U virtuálního počítače Azure můžete [Zastavit ochranu s uchováním dat](backup-azure-manage-vms.md#stop-protecting-a-vm) pro virtuální počítač ve starém trezoru, přesunout virtuální počítač do jiné skupiny prostředků a pak chránit virtuální počítač v novém trezoru. Přečtěte si [doprovodné materiály a omezení](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) pro přesun virtuálního počítače do jiné skupiny prostředků.

  Virtuální počítač se dá v jednom okamžiku chránit jenom v jednom trezoru. Virtuální počítač v nové skupině prostředků se ale dá chránit v novém trezoru, protože se považuje za jiný virtuální počítač.

  - Služba Azure Backup zachová body obnovení, které byly zálohovány ve starém trezoru.
  - Abyste zachovali body obnovení ve starém trezoru (podrobnosti najdete v tématu [Azure Backup ceny](azure-backup-pricing.md) ).
  - V případě potřeby budete moct virtuální počítač obnovit ze starého trezoru.
  - První záloha v novém trezoru virtuálního počítače v novém prostředku bude počáteční replikou.

## <a name="next-steps"></a>Další kroky

Mezi skupinami prostředků a předplatnými můžete přesunout mnoho různých typů prostředků.

Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).