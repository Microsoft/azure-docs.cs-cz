---
title: Spustit připojení k virtuálnímu počítači – Azure
description: Postup konfigurace funkce spustit virtuální počítač při připojení
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: d9ce9811cd660c9a9b1fcb6f9e24cadd65d3c2bc
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445597"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Spustit virtuální počítač na Connect (Preview)

> [!IMPORTANT]
> Funkce spustit virtuální počítač v Connect je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkce spustit virtuální počítač (VM) na Connect (Preview) umožňuje ušetřit náklady tím, že vám umožní zrušit přidělení virtuálních počítačů, když je nebudete používat. Pokud budete chtít virtuální počítač znovu použít, stačí, když znovu zapnete své virtuální počítače.

>[!NOTE]
>Virtuální plocha Windows (Classic) tuto funkci nepodporuje.

## <a name="requirements-and-limitations"></a>Požadavky a omezení

Pro fondy osobních hostitelů můžete povolit jenom funkci spustit virtuální počítač při připojení. Další informace o fondech osobních hostitelů najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup.md#host-pools).

Následující klienti vzdálené plochy podporují funkci spustit virtuální počítač při připojení:

- [Webový klient](connect-web.md)
- [Klient Windows (verze 1,2748 nebo novější)](connect-windows-7-10.md)

Můžete si prohlédnout oznámení o aktualizacích a podpoře klientů na fóru pro pracovníky odborné [komunity](https://aka.ms/wvdtc).

>[!IMPORTANT]
>Funkce spustit virtuální počítač v Connect v současné době podporuje jenom PowerShell a REST API, ne Azure Portal. Další informace najdete v tématu [Vytvoření nebo aktualizace fondu hostitelů](/rest/api/desktopvirtualization/hostpools/createorupdate).

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Vytvoření vlastní role pro spuštění virtuálního počítače v Connect

Než budete moct nakonfigurovat funkci spustit virtuální počítač při připojení, bude nutné přiřadit VIRTUÁLNÍmu počítači vlastní roli RBAC (řízení přístupu na základě role). Tato role umožní virtuálním plochám Windows správu virtuálních počítačů ve vašem předplatném. Tuto roli můžete použít také k zapnutí virtuálních počítačů, kontrole jejich stavu a k hlášení diagnostických informací. Pokud se chcete dozvědět více o tom, co jednotlivé role využívají, podívejte se na [vlastní role Azure](../role-based-access-control/custom-roles.md).

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Chcete-li použít Azure Portal k přiřazení vlastní role pro spouštěcí virtuální počítač v Connect:

1. Otevřete Azure Portal a pokračujte na **předplatná**.

2. Přejděte na **řízení přístupu (IAM)** a vyberte **Přidat vlastní roli**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s rozevírací nabídkou z tlačítka Přidat v řízení přístupu (IAM). Možnost přidat vlastní roli je zvýrazněná červeně.](media/add-custom-role.png)

3. Dále pojmenujte vlastní roli a přidejte popis. Doporučujeme, abyste si pojmenovat "spuštění virtuálního počítače při připojení".

4. Na kartě **oprávnění** přidejte do předplatného, ke kterému roli přiřazujete, následující oprávnění: 
 
   - Microsoft. COMPUTE/virtualMachines/Start/Action
   - Microsoft. COMPUTE/virtualMachines/Read

5. Až budete hotovi, vyberte **OK**.

Potom budete muset přiřadit roli pro udělení přístupu k virtuálnímu počítači s Windows.

Přiřazení vlastní role:

1. Na **kartě řízení přístupu (IAM)** vyberte **Přidat přiřazení rolí**.

2. Vyberte roli, kterou jste právě vytvořili.

3. Na panelu hledání zadejte a vyberte **virtuální počítač s Windows**.

      >[!NOTE]
      >Pokud jste nasadili virtuální plochu Windows (Classic), můžou se zobrazit dvě aplikace. Přiřaďte roli oběma aplikacím, které vidíte.
      >
      > [!div class="mx-imgBorder"]
      > ![Snímek obrazovky s kartou pro řízení přístupu (IAM). Na panelu hledání jsou červeně zvýrazněni virtuální klient Windows i virtuální plocha Windows (Classic).](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Vytvoření vlastní role se šablonou souborů JSON

Pokud k vytvoření vlastní role používáte soubor JSON, následující příklad ukazuje základní šablonu, kterou můžete použít. Ujistěte se, že jste nahradili hodnotu ID předplatného s ID předplatného, ke kterému chcete přiřadit roli.

```json
{
    "properties": {
        "roleName": "start VM on connect",
        "description": "Friendly description.",
        "assignableScopes": [
            "/subscriptions/<SubscriptionID>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/virtualMachines/start/action",
                    "Microsoft.Compute/virtualMachines/read"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="configure-the-start-vm-on-connect-feature"></a>Konfigurace funkce spustit virtuální počítač při připojení

Teď, když jste přiřadili své předplatné k této roli, je čas nakonfigurovat funkci spustit virtuální počítač při připojení.

### <a name="deployment-considerations"></a>Aspekty nasazování 

Možnost spustit virtuální počítač v Connect je nastavení fondu hostitelů. Pokud chcete použít tuto funkci jenom pro výběr skupiny uživatelů, ujistěte se, že přiřadíte požadovanou roli jenom uživatelům, které chcete přidat.

>[!IMPORTANT]
> Tuto funkci můžete nakonfigurovat jenom v existujících fondech hostitelů. Tato funkce není k dispozici, když vytváříte nový fond hostitelů.

### <a name="use-powershell"></a>Použití prostředí PowerShell

Pokud chcete toto nastavení nakonfigurovat pomocí PowerShellu, musíte se ujistit, že máte názvy skupin prostředků a fondů hostitelů, které chcete nakonfigurovat. Budete také muset nainstalovat [modul Azure PowerShell (verze 2.1.0 nebo novější)](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0).

Konfigurace spuštění virtuálního počítače na Connect pomocí prostředí PowerShell:

1. Otevřete okno příkazového řádku PowerShellu.

2. Spuštěním následující rutiny povolte možnost spustit virtuální počítač v Connect:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Spuštěním následující rutiny zakažte spouštěcí virtuální počítač v Connect:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Uživatelské prostředí

V typických relacích se zvyšuje čas potřebný k připojení uživatele k uvolněnému VIRTUÁLNÍmu počítači, protože virtuální počítač potřebuje čas na zapnutí, podobně jako při zapnutí fyzického počítače. Klient vzdálené plochy má indikátor, který uživateli ví, že je počítač zapnutý, zatímco se připojuje.

## <a name="troubleshooting"></a>Řešení potíží

Pokud se funkce vyskytne v jakémkoli problému, doporučujeme, abyste k vyhledání problémů použili [funkci diagnostiky](diagnostics-log-analytics.md) virtuálních počítačů s Windows. Pokud se zobrazí chybová zpráva, nezapomeňte se podívat na obsah zprávy a zkopírovat název chyby někam pro referenci.

K získání návrhů na řešení problémů můžete použít také [Azure monitor pro virtuální počítač s Windows](azure-monitor.md) .

## <a name="next-steps"></a>Další kroky

Pokud narazíte na problémy, které se v dokumentaci pro řešení potíží nebo diagnostické funkci nepovedlo vyřešit, podívejte se na [Nejčastější dotazy ke spouštěcímu virtuálnímu počítači](start-virtual-machine-connect-faq.md).