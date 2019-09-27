---
title: Přesunutí trezoru Recovery Services mezi předplatnými Azure nebo skupinami prostředků
description: Pokyny pro přesun trezoru služby Recovery Services napříč předplatnými Azure a skupinami prostředků.
ms.reviewer: sogup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: dacurwin
ms.openlocfilehash: 6e95c012aed9fdcfda2b64c310458425df2b9f9e
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337894"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Přesunutí trezoru Recovery Services napříč předplatnými Azure a skupinami prostředků

Tento článek vysvětluje, jak přesunout trezor Recovery Services nakonfigurovaný pro Azure Backup v rámci předplatných Azure nebo do jiné skupiny prostředků ve stejném předplatném. K přesunutí trezoru Recovery Services můžete použít Azure Portal nebo PowerShell.

## <a name="supported-region"></a>Podporovaná oblast

Přesun prostředků pro úložiště Recovery Services se podporuje v oblasti Austrálie – východ, Austrálie – jih, Kanada – střed, Kanada – východ, Jižní Východní Asie, Východní Asie, Střed USA, Střed USA – sever, Východní USA, východní USA 2, střed USA, Středozápadní USA, Západ Střední USA 2, Západní USA, Střed Indie, Jižní Indie, Japonsko – východ, Japonsko – západ, Korea – jih, Korea – jih, Severní Evropa, Západní Evropa, Jižní Afrika sever, Jižní Afrika – západ, Velká Británie – jih a Velká Británie – západ.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Předpoklady pro přesunutí trezoru Recovery Services

- Při přesunu trezoru mezi skupinami prostředků jsou zdrojové i cílové skupiny prostředků zamčené, aby se zabránilo operacím zápisu a odstranění. Další informace najdete v tomto [článku](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Pouze předplatné správce má oprávnění k přesunu trezoru.
- Pro přesun trezoru mezi předplatnými se musí cílové předplatné nacházet ve stejném tenantovi jako zdrojové předplatné a jeho stav by měl být povolený.
- Musíte mít oprávnění k provádění operací zápisu v cílové skupině prostředků.
- Přesun trezoru mění jenom skupinu prostředků. Trezor Recovery Services se bude nacházet ve stejném umístění a nelze ho změnit.
- V jednom okamžiku můžete přesunout jenom jeden Recovery Services trezor pro oblast.
- Pokud se virtuální počítač nepřesouvá s Recovery Servicesovým trezorem v rámci předplatných nebo na novou skupinu prostředků, v trezoru zůstanou v trezoru nedotčené, dokud nevyprší jejich platnost.
- Bez ohledu na to, jestli se virtuální počítač přesune do trezoru, nebo ne, můžete virtuální počítač vždycky obnovit z historie uchovávané zálohy v trezoru.
- Azure Disk Encryption vyžaduje, aby se Trezor klíčů a virtuální počítače nacházely ve stejné oblasti a předplatném Azure.
- Postup přesunutí virtuálního počítače se spravovanými disky najdete v tomto [článku](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- Možnosti přesunutí prostředků nasazených přes klasický model se liší v závislosti na tom, zda přesouváte prostředky v rámci předplatného nebo do nového předplatného. Další informace najdete v tomto [článku](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Zásady zálohování definované pro trezor se uchovávají po přesunu trezoru mezi předplatnými nebo do nové skupiny prostředků.
- Přesun trezoru se soubory Azure, Azure File Sync nebo SQL ve virtuálních počítačích IaaS napříč předplatnými a skupinami prostředků se nepodporuje.
- Pokud přesunete trezor obsahující data záloh virtuálních počítačů v rámci předplatných, musíte virtuální počítače přesunout do stejného předplatného, a pokud chcete pokračovat v zálohování, použijte stejný název cílové skupiny prostředků virtuálního počítače (stejně jako ve starém předplatném).<br>

> [!NOTE]
>
> Trezory Recovery Services nakonfigurované pro použití s **Azure Site Recovery** se ještě nedají přesunout. Pokud jste nakonfigurovali nějaké virtuální počítače (Azure IaaS, Hyper-V, VMware) nebo fyzické počítače pro zotavení po havárii pomocí **Azure Site Recovery**, operace přesunu se zablokuje. Funkce přesunutí prostředku pro službu Site Recovery ještě není dostupná.


## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Použití Azure Portal k přesunu Recovery Services trezoru do jiné skupiny prostředků

Přesun trezoru služby Recovery Services a přidružených prostředků do jiné skupiny prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Otevřete seznam trezorů **Recovery Services** a vyberte trezor, který chcete přesunout. Po otevření řídicího panelu trezoru se zobrazí, jak je znázorněno na následujícím obrázku.

   ![Otevřít Recovery Service Recovery](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Pokud nevidíte **základní** informace pro váš trezor, klikněte na ikonu rozevíracího seznamu. Teď byste měli vidět základní informace o vašem trezoru.

   ![Karta informace o Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. V nabídce přehled trezoru klikněte na **změnit** vedle **skupiny prostředků**a otevřete okno **přesunout prostředky** .

   ![Změnit skupinu prostředků](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. V okně **přesunout prostředky** pro vybraný trezor doporučujeme přesunout volitelné související prostředky tak, že vyberete zaškrtávací políčko, jak je znázorněno na následujícím obrázku.

   ![Přesunout předplatné](./media/backup-azure-move-recovery-services/move-resource.png)

5. Chcete-li přidat cílovou skupinu prostředků, v rozevíracím seznamu **Skupina prostředků** vyberte existující skupinu prostředků nebo klikněte na možnost **vytvořit novou skupinu** .

   ![Vytvořit prostředek](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Po přidání skupiny prostředků potvrďte **, že nástroje a skripty přidružené k přesunutým prostředkům nebudou fungovat, dokud je neaktualizujete tak, aby používaly nové ID prostředků** , a pak kliknutím na **OK** dokončete přesun trezoru.

   ![Potvrzovací zpráva](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Použití Azure Portal k přesunu Recovery Services trezoru do jiného předplatného

Můžete přesunout Recovery Services trezor a jeho přidružené prostředky do jiného předplatného.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Otevřete seznam trezorů Recovery Services a vyberte trezor, který chcete přesunout. Po otevření řídicího panelu trezoru se zobrazí, jak je znázorněno na následujícím obrázku.

    ![Otevřít Recovery Service Recovery](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Pokud nevidíte **základní** informace pro váš trezor, klikněte na ikonu rozevíracího seznamu. Teď byste měli vidět základní informace o vašem trezoru.

    ![Karta informace o Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. V nabídce přehled trezoru klikněte na **změnit** vedle předplatného. otevře se okno **přesunout prostředky** .

   ![Změnit předplatné](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Vyberte prostředky, které se mají přesunout. tady doporučujeme, abyste pro výběr všech uvedených volitelných prostředků použili možnost **Vybrat vše** .

   ![přesunout prostředek](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. V rozevíracím seznamu **odběr** vyberte cílové předplatné, kde chcete trezor přesunout.
6. Chcete-li přidat cílovou skupinu prostředků, v rozevíracím seznamu **Skupina prostředků** vyberte existující skupinu prostředků nebo klikněte na možnost **vytvořit novou skupinu** .

   ![Přidat předplatné](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Klikněte na možnost **beru na představu, že nástroje a skripty přidružené k přesunutým prostředkům nebudou fungovat, dokud je neaktualizujete tak, aby k potvrzení používaly nové identifikátory prostředků** , a pak klikněte na **OK**.

> [!NOTE]
> Zálohování mezi předplatnými (trezor RS a chráněné virtuální počítače jsou v různých předplatných) není podporovaným scénářem. Možnost redundance úložiště z místního redundantního úložiště (LRS) do globálního redundantního úložiště (GRS) a naopak se nedá změnit během operace přesunu trezoru.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Použití PowerShellu k přesunutí trezoru Recovery Services

Pokud chcete přesunout Recovery Services trezor do jiné skupiny prostředků, použijte `Move-AzureRMResource` rutinu. `Move-AzureRMResource`vyžaduje název prostředku a typ prostředku. Z `Get-AzureRmRecoveryServicesVault` rutiny můžete získat obojí.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Chcete-li přesunout prostředky do jiného předplatného `-DestinationSubscriptionId` , zahrňte parametr.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Po provedení výše uvedených rutin budete požádáni o potvrzení, že chcete přesunout zadané prostředky. Zadáním **Y** potvrďte. Po úspěšném ověření se prostředek přesune.

## <a name="use-cli-to-move-recovery-services-vault"></a>Přesunutí Recovery Services trezoru pomocí rozhraní příkazového řádku

Pokud chcete přesunout Recovery Services trezor do jiné skupiny prostředků, použijte tuto rutinu:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Pokud chcete přesunout do nového předplatného, zadejte `--destination-subscription-id` parametru.

## <a name="post-migration"></a>Po migraci

1. Musíte nastavit nebo ověřit řízení přístupu pro skupiny prostředků.  
2. Po dokončení přesunu se musí znovu nakonfigurovat funkce pro vytváření sestav a monitorování zálohování pro trezor. Předchozí konfigurace bude ztracena během operace přesunutí.



## <a name="next-steps"></a>Další kroky

Mezi skupinami prostředků a předplatnými můžete přesunout mnoho různých typů prostředků.

Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
