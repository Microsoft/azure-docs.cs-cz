---
title: Přesunutí trezorů služby Azure Backup Recovery Services
description: Pokyny, jak přesunout trezor služeb pro obnovení mezi předplatnými Azure a skupinami prostředků.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: fed42c578da2e4f27f42e11d5ac67d698bbcd939
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120723"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Přesunutí trezoru služby Recovery Services mezi předplatnými Azure a skupinami prostředků

Tento článek vysvětluje, jak přesunout trezor služby Recovery Services nakonfigurovaný pro Azure Backup napříč předplatnými Azure nebo do jiné skupiny prostředků ve stejném předplatném. K přesunutí trezoru služby Recovery Services můžete použít portál Azure nebo Prostředí PowerShell.

## <a name="supported-regions"></a>Podporované oblasti

Přesun zdrojů pro trezor služby Recovery Services je podporován v Austrálii – východ, Austrálie – jihovýchod, Kanada – střed, Kanada – východ, Jihovýchodní Asie, Východní Asie, Střední USA, Střed USA, Střed USA, Východní USA, Východní USA2, Střed USA – jih, Střed USA – západ, STŘED USA2, Západní USA, Střední Indie, Jižní Indie, Japonsko – východ, Japonsko – západ, Korea – střed, Korea – jih, severní Evropa, západní Evropa, Jižní Afrika – sever, Jižní Afrika – západ, Velká Británie – jih a Spojené království – západ.

## <a name="unsupported-regions"></a>Nepodporované oblasti

Francie – střed, Francie – jih, Německo – severovýchod, Německo – střed, vláda USA Iowa, Čína – sever2, Čína – východ, Čína – východ2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Předpoklady pro přesunutí trezoru služby Recovery Services

- Během přesunu úschovny mezi skupinami prostředků jsou skupiny zdrojových i cílových prostředků uzamčeny a zabraňují operacím zápisu a odstraňování. Další informace naleznete v tomto [článku](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Oprávnění k přesunu trezoru má pouze předplatné správce.
- Pro přesunutí trezoru mezi předplatnými musí být cílové předplatné umístěno ve stejném tenantovi jako zdrojové předplatné a jeho stav by měl být povolen.
- Musíte mít oprávnění k provádění operací zápisu v cílové skupině prostředků.
- Přesunutím úložiště se změní pouze skupina prostředků. Trezor služby Recovery Services bude umístěn ve stejném umístění a nelze jej změnit.
- Současně můžete přesunout pouze jeden trezor služby Recovery Services pro oblast.
- Pokud se virtuální virtuální město nepřesune s trezorem služby Recovery Services napříč předplatnými nebo do nové skupiny prostředků, zůstanou aktuální body obnovení virtuálních ms v trezoru beze změny, dokud nevyprší jejich platnost.
- Bez ohledu na to, zda je virtuální virtuální počítače přesunuta s trezorem nebo ne, můžete vždy obnovit virtuální ho z historie zachované zálohy v trezoru.
- Azure Disk Encryption vyžaduje, aby trezor klíčů a virtuální počítače jsou umístěny ve stejné oblasti Azure a předplatné.
- Pokud chcete přesunout virtuální počítač se spravovanými disky, přečtěte si tento [článek](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- Možnosti pro přesunutí prostředků nasazených prostřednictvím modelu Classic se liší v závislosti na tom, zda přesouváte prostředky v rámci předplatného nebo na nové předplatné. Další informace naleznete v tomto [článku](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Zásady zálohování definované pro trezor se zachovají i po přesunu trezoru mezi předplatnými nebo do nové skupiny prostředků.
- Přesunutí trezoru s Azure Files, Azure File Sync nebo SQL ve virtuálních počítačích IaaS napříč předplatnými a skupinami prostředků není podporované.
- Pokud přesunete trezor obsahující data zálohování virtuálních počítačových společností, napříč předplatnými, musíte přesunout virtuální počítače do stejného předplatného a použít stejný název skupiny prostředků cílového virtuálního počítače (jako tomu bylo ve starém předplatném) k pokračování zálohování.

> [!NOTE]
>
> Trezory služby Recovery Services nakonfigurované pro použití s **Azure Site Recovery** se zatím nemůžou přesunout. Pokud jste nakonfigurovali všechny virtuální počítače (Azure IaaS, Hyper-V, VMware) nebo fyzické počítače pro zotavení po havárii pomocí **Azure Site Recovery**, operace přesunutí se bude blokovat. Funkce přesunutí prostředků pro službu Obnovení webu ještě není k dispozici.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Přesunutí trezoru služby Recovery Services do jiné skupiny prostředků pomocí portálu Azure Portal

Přesunutí trezoru služeb pro obnovení a přidružených prostředků do jiné skupiny prostředků

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Otevřete seznam **trezorů služby Recovery Services** a vyberte trezor, který chcete přesunout. Když se řídicí panel trezoru otevře, zobrazí se tak, jak je znázorněno na následujícím obrázku.

   ![Otevřít trezor obnovení služby](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Pokud informace o **základních informacích** pro svůj trezor nevidíte, klikněte na ikonu rozevíracího panelu. Nyní byste měli vidět základní informace pro váš trezor.

   ![Karta Základní informace](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. V nabídce přehled úschovny klikněte na **změnit** vedle **skupiny Prostředků**a otevřete okno **Přesunout prostředky.**

   ![Změnit skupinu prostředků](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. V okně **Přesunout prostředky** se pro vybraný trezor doporučuje přesunout volitelné související prostředky zaškrtnutím políčka, jak je znázorněno na následujícím obrázku.

   ![Přesunout předplatné](./media/backup-azure-move-recovery-services/move-resource.png)

5. Chcete-li přidat cílovou skupinu prostředků, vyberte v rozevíracím seznamu **Skupina prostředků** existující skupinu prostředků nebo klepněte na tlačítko Vytvořit novou možnost **skupiny.**

   ![Vytvořit zdroj](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Po přidání skupiny prostředků potvrďte, **že chápu, že nástroje a skripty přidružené k přesunutým prostředkům nebudou fungovat, dokud je neaktualizuji, aby používaly nové ID prostředků,** a potom klepnutím na tlačítko **OK** dokončení přesunutí úložiště.

   ![Potvrzovací zpráva](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Přesunutí trezoru služby Recovery Services do jiného předplatného pomocí portálu Azure Portal

Trezor služby Recovery Services a jeho přidružené prostředky můžete přesunout do jiného předplatného.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Otevřete seznam trezorů služby Recovery Services a vyberte trezor, který chcete přesunout. Když se řídicí panel trezoru otevře, zobrazí se tak, jak je znázorněno na následujícím obrázku.

    ![Otevřít trezor obnovení služby](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Pokud informace o **základních informacích** pro svůj trezor nevidíte, klikněte na ikonu rozevíracího panelu. Nyní byste měli vidět základní informace pro váš trezor.

    ![Karta Základní informace](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. V nabídce přehled u trezoru klikněte na **změnit** vedle **položky Odběr**, otevřete okno **Přesunout prostředky.**

   ![Změna předplatného](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Vyberte zdroje, které mají být přesunuty, zde doporučujeme použít **možnost Vybrat vše** a vybrat všechny uvedené volitelné zdroje.

   ![přesunutí zdroje](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. V rozevíracím seznamu **Odběr** vyberte cílové předplatné, ve kterém chcete trezor přesunout.
6. Chcete-li přidat cílovou skupinu prostředků, vyberte v rozevíracím seznamu **Skupina prostředků** existující skupinu prostředků nebo klepněte na tlačítko Vytvořit novou možnost **skupiny.**

   ![Přidat předplatné](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Klepněte na **tlačítko Chápu, že nástroje a skripty spojené s přesunutými zdroji nebudou fungovat, dokud je neaktualizuji, aby bylo možné použít novou id zdrojů,** a poté klepněte na tlačítko **OK**.

> [!NOTE]
> Zálohování mezi předplatnými (trezor RS a chráněné virtuální počítače jsou v různých předplatných) není podporovaný scénář. Možnost redundance úložiště z místního redundantního úložiště (LRS) na globální redundantní úložiště (GRS) a naopak nelze změnit během operace přesunutí úložiště.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Přesunutí trezoru služby Recovery Services pomocí prostředí PowerShell

Chcete-li přesunout trezor služby Recovery `Move-AzureRMResource` Services do jiné skupiny prostředků, použijte rutinu. `Move-AzureRMResource`vyžaduje název zdroje a typ prostředku. Můžete získat obojí `Get-AzureRmRecoveryServicesVault` z rutiny.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Chcete-li přesunout prostředky do `-DestinationSubscriptionId` jiného předplatného, zadejte parametr.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Po provedení výše uvedených rutin budete vyzváni k potvrzení, že chcete přesunout zadané prostředky. Potvrďte to zadejte **Y.** Po úspěšném ověření se prostředek přesune.

## <a name="use-cli-to-move-recovery-services-vault"></a>Přesunutí trezoru služby Recovery Services pomocí příkazu příkazu příkazu příkazu

Chcete-li přesunout trezor služby Recovery Services do jiné skupiny prostředků, použijte následující rutinu:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Chcete-li přejít na `--destination-subscription-id` nové předplatné, zadejte parametr.

## <a name="post-migration"></a>Po dokončení migrace

1. Nastavte nebo ověřte ovládací prvky přístupu pro skupiny prostředků.  
2. Funkce zálohování sestav a monitorování musí být znovu nakonfigurována pro příspěvek trezoru, který je dokončen. Předchozí konfigurace bude během operace přesunu ztracena.

## <a name="next-steps"></a>Další kroky

Můžete přesunout mnoho různých typů prostředků mezi skupinami prostředků a odběry.

Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
