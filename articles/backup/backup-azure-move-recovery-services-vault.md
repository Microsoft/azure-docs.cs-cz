---
title: Přesun trezoru služby Recovery Services napříč předplatnými Azure nebo do jiné skupiny prostředků
description: Pokyny k přesunutí služby recovery services vault napříč skupinami prostředků a předplatných azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2019
ms.author: sogup
ms.openlocfilehash: e1df91a11a474faf3a10dbbb7c99ea058037d685
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54108143"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups-limited-public-preview"></a>Přesun trezoru služby Recovery Services napříč předplatnými Azure a skupiny prostředků (omezené veřejné verzi Preview)

Tento článek vysvětluje, jak přesunout trezor služby Recovery Services, který je nakonfigurovaný pro službu Azure Backup napříč předplatnými Azure nebo do jiné skupiny prostředků ve stejném předplatném. Na webu Azure portal nebo prostředí PowerShell můžete použít pro přesun trezoru služby Recovery Services.

## <a name="prerequisites-for-moving-a-vault"></a>Předpoklady pro přesun trezoru

- Při přesouvání mezi skupinami prostředků, jsou během operace uzamčení zdrojovou skupinu prostředků a cílovou skupinu prostředků. Až do dokončení přesunu, zapisovat a odstraňovat operace jsou blokovány o skupinách prostředků.
- Pouze správce předplatného má oprávnění pro přesun trezoru.
- Při přesunu napříč předplatnými trezor, cílové předplatné musí existovat v povoleném stavu a musí být ve stejném tenantovi jako zdrojové předplatné.
- Musíte mít oprávnění k provádění operací zápisu na cílovou skupinu prostředků.
- Nelze změnit umístění trezoru služby Recovery Services. Přesun trezoru se změní jenom skupinu prostředků. Nová skupina prostředků může být v jiném umístění, ale to se nemění umístění v úložišti.
- Aktuálně můžete přesunout jeden trezor služby Recovery Services na oblast, najednou.
- Pokud virtuální počítač čárka nepohybuje trezor služby Recovery Services napříč předplatnými, nebo chcete novou skupinu prostředků, aktuálním bodům obnovení virtuálního počítače zůstanou beze změn v trezoru, dokud nevyprší jejich platnost.
- Zda virtuální počítač se přesune k trezoru, nebo Ne, můžete vždy obnovit virtuální počítač ze historii uchovávané zálohy v trezoru.
-   Azure Disk Encryption vyžaduje, aby váš trezor klíčů a virtuální počítače jsou umístěny ve stejné oblasti Azure a předplatné.
-   Pokud chcete přesunout virtuální počítač se spravovanými disky, najdete v tomto [článku](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
-   Možnosti pro přesun prostředky nasazené prostřednictvím klasického modelu, se liší v závislosti na tom, jestli přesouváte prostředky v rámci předplatného nebo do nového předplatného. Další informace najdete v tomto [článku](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#classic-deployment-limitations).
-   Zásady zálohování, které jsou definovány pro trezor se uchovávají po přesunutí úložiště napříč předplatnými nebo do nové skupiny prostředků.
-   V současné době nelze přesunout trezory obsahující soubory Azure, Azure File Sync nebo jazyka SQL ve virtuální počítače IaaS napříč předplatné a skupiny prostředků. Podpora pro tyto scénáře budou přidávat do budoucích verzí.
-   Pokud přesunete trezor obsahující data záloh virtuálních počítačů mezi předplatnými, musíte přesunout virtuální počítače do stejného předplatného a pokračovat v zálohování pomocí stejné cílová skupina prostředků.<br>

> [!NOTE]
>
Konfigurovány k použití s trezory služby Recovery Services **Azure Site Recovery** nelze přesunout ještě. Pokud jste nakonfigurovali všechny virtuální počítače (Azure IaaS, technologie Hyper-V, VMware) nebo fyzické počítače pro použití pro zotavení po havárii **Azure Site Recovery**, operace přesunu se zablokuje. Funkci přesunu prostředků pro službu Site Recovery není k dispozici.
>
>

## <a name="register-the-subscription-to-move-your-recovery-services-vault"></a>Zaregistrovat předplatné pro přesun trezoru služby Recovery Services

Pro dané předplatné zaregistrujete do **přesunout** trezoru služby Recovery Services, spusťte následující rutiny Powershellu terminálu:

1. Přihlášení k účtu Azure

  ```
  Connect-AzureRmAccount
  ```

2.  Vyberte předplatné, pro kterou chcete zaregistrovat

    ```
    Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```
3.  Zaregistrujte toto předplatné

  ```
  Register-AzureRmProviderFeature -ProviderNamespace Microsoft.RecoveryServices -FeatureName RecoveryServicesResourceMove
  ```

Počkejte 30 minut, než předplatného na seznam povolených předtím, než začnete s přesunutím pomocí webu Azure portal nebo Powershellu.

## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-different-resource-group"></a>Přesunout do jiné skupiny prostředků trezoru služby Recovery Services pomocí webu Azure portal

Chcete-li přesunout obnovení služby trezor a její přidružené prostředky do jiné skupiny prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Otevře se seznam **trezory služby Recovery Services** a vyberte trezor, které chcete přesunout. Po otevření řídicího panelu trezoru, zobrazí se jak je znázorněno na následujícím obrázku.

  ![Trezor otevřete obnovení služby](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

  Pokud se nezobrazí **Essentials** informace o trezoru, klikněte na ikonu rozevíracího seznamu. Teď byste měli vidět Essentials informace pro svůj trezor.

  ![Na kartě informací](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. V nabídce přehled trezoru, klikněte na tlačítko **změnit** vedle **skupiny prostředků**, otevřete **přesunutí prostředků** okno.

  ![Změnit skupinu prostředků](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. V **přesunutí prostředků** okně pro vybraný trezor se doporučuje se přesunout volitelné související prostředky zaškrtnutím políčka, jak je znázorněno na následujícím obrázku.

  ![Přesunout předplatné](./media/backup-azure-move-recovery-services/move-resource.png)

5. Přidání cílová skupina prostředků v **skupiny prostředků** rozevíracího seznamu vyberte existující prostředek skupiny nebo klikněte na tlačítko **vytvořte novou skupinu** možnost.

  ![Vytvořit prostředek](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Po přidání skupiny prostředků, zkontrolujte **beru na vědomí, že nástroje a skripty přidružené k přesunutým prostředkům nebudou fungovat, dokud můžu aktualizovat je, aby používaly nové ID prostředků** možnost a potom klikněte na tlačítko **OK** k dokončení Přesun trezoru.

  ![Zpráva potvrzení](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-a-different-subscription"></a>Přesun trezoru služby Recovery Services do jiného předplatného pomocí webu Azure portal

Trezor služby Recovery Services a její přidružené prostředky můžete přesunout do jiného předplatného

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Otevře se seznam trezorů služby Recovery Services a vyberte trezor, který chcete přesunout. Po otevření řídicího panelu trezoru, zobrazí se jak je znázorněno na následujícím obrázku.

    ![Trezor otevřete obnovení služby](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Pokud se nezobrazí **Essentials** informace o trezoru, klikněte na ikonu rozevíracího seznamu. Teď byste měli vidět Essentials informace pro svůj trezor.

    ![Na kartě informací](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. V nabídce přehled trezoru, klikněte na tlačítko **změnit** vedle **předplatné**, otevřete **přesunutí prostředků** okno.

  ![Změnit předplatné](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Vyberte prostředky k přesunutí, tady doporučujeme, abyste použili **Vybrat vše** možnost vybrat si uvedené volitelné materiály.

  ![Přesunout prostředek](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Vyberte cílovou skupinu, ze **předplatné** rozevíracího seznamu, kam chcete přesunout na trezor.
6. Přidání cílová skupina prostředků v **skupiny prostředků** rozevíracího seznamu vyberte existující prostředek skupiny nebo klikněte na tlačítko **vytvořte novou skupinu** možnost.

  ![Přidat předplatné](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Klikněte na tlačítko **beru na vědomí, že nástroje a skripty přidružené k přesunutým prostředkům nebudou fungovat, dokud můžu aktualizovat je, aby používaly nové ID prostředků** potvrzení, a potom klikněte na možnost **OK**.

> [!NOTE]
> Pro různé předplatného zálohování (RS trezor a chráněné virtuální počítače jsou v různých předplatných) není podporovaný scénář. Redundance úložiště také možnost místní redundantní úložiště (LRS) do globální redundantního úložiště (GRS) a naopak je nelze změnit během operace přesunu úložiště.
>
>

## <a name="use-powershell-to-move-a-vault"></a>Přesun trezoru pomocí Powershellu

Chcete-li přesunout do jiné skupiny prostředků trezoru služby Recovery Services, použijte `Move-AzureRMResource` rutiny. `Move-AzureRMResource` vyžaduje, aby název prostředku a typ prostředku. Můžete získat i z `Get-AzureRmRecoveryServicesVault` rutiny.

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Prostředky přesunout do jiného předplatného, zahrňte `-DestinationSubscriptionId` parametru.

```
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Po provedení výše uvedených rutin, budou vyzváni k potvrzení, že chcete přesunout zadané prostředky. Typ **Y** potvrďte. Po úspěšném ověření že se prostředek přesune.

## <a name="use-cli-to-move-a-vault"></a>Přesun trezoru pomocí rozhraní příkazového řádku

Trezor služby Recovery Services se přesunout do jiné skupiny prostředků, použijte následující rutinu:

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Pokud chcete přesunout do nového předplatného, zadejte `--destination-subscription-id` parametru.

## <a name="post-migration"></a>Po dokončení migrace

1. Budete muset sadu/ověření řízení přístupu pro skupiny prostředků.  
2. Zálohování pro generování sestav a monitorování funkce je potřeba znovu nakonfigurovat příspěvek trezor, které se přesun dokončí. Předchozí konfigurace budou ztraceny během operace přesunu.



## <a name="next-steps"></a>Další postup

Mnoho různých typů prostředků můžou přesouvat mezi skupinami prostředků a předplatných.

Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
