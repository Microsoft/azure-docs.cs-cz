---
title: Kurz – Vytvoření a správa exportovaných dat ze služby Azure Cost Management
description: Tento článek popisuje, jak vytvořit a spravovat exportovaná data služby Azure Cost Management tak, aby se dala používat v externích systémech.
author: bandersmsft
ms.author: banders
ms.date: 12/7/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: e3c1fa071cd23b871f754e89d6f17eb2cc44b394
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400348"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Kurz: Vytvoření a správa exportovaných dat

Pokud jste si prošli kurz Analýza nákladů, pak jste obeznámeni s ručním stahováním dat ze služby Cost Management. Můžete však vytvořit opakující se úlohu, která denně, týdně nebo měsíčně automaticky exportuje data služby Cost Management do úložiště Azure. Exportovaná data jsou ve formátu CSV a obsahují všechny informace, které služba Cost Management shromažďuje. Exportovaná data v úložišti Azure pak můžete používat s externími systémy a kombinovat je se svými vlastními daty. Exportovaná data můžete používat v externím systému, jako je třeba řídicí panel nebo jiný finanční systém.

Podívejte se na video [How to schedule exports to storage with Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) (Jak naplánovat exporty do úložiště pomocí služby Azure Cost Management), které se věnuje vytváření naplánovaného exportu dat nákladů na Azure do Azure Storage. Další videa najdete v [kanálu služby Cost Management na YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

Příklady v tomto kurzu vás provedou exportem dat služby Cost Management a následným ověřením, že se daná data úspěšně exportovala.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit každodenní export
> * Ověřit shromáždění dat

## <a name="prerequisites"></a>Požadavky
Export dat je dostupný pro nejrůznější typy účtů Azure, včetně zákazníků se [smlouvou Enterprise ](https://azure.microsoft.com/pricing/enterprise-agreement/) a zákazníků se [Smlouvou se zákazníkem Microsoftu](get-started-partners.md). Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku, který [vysvětluje data služby Cost Management](understand-cost-mgt-data.md). Při exportu dat uživatelů a skupin se u předplatných podporují následující oprávnění, neboli obory, Azure. Další informace o oborech najdete v tématu [Principy oborů a práce s nimi](understand-work-scopes.md).

- Vlastník – může vytvářet, upravovat a odstraňovat naplánované exporty u předplatných.
- Přispěvatel – může vytvářet, upravovat a odstraňovat svoje vlastní naplánované exporty. Může měnit názvy naplánovaných exportů, které vytvořili jiní uživatelé.
- Čtenář – může naplánovat exporty, ke kterým má oprávnění.

Pro účty služby Azure Storage:
- Ke změně nakonfigurovaného účtu úložiště se vyžaduje oprávnění k zápisu, a to bez ohledu na oprávnění k exportu.
- Příslušný účet úložiště Azure musí být nakonfigurovaný jako úložiště souborů nebo objektů blob.

Pokud máte nové předplatné, nemůžete rovnou využívat funkce služby Cost Management. Může trvat až 48 hodin, než budete moct využívat všechny funkce služby Cost Management.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Vytvořit každodenní export

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete vytvořit nebo zobrazit export dat nebo naplánovat export, otevřete požadovaný obor na webu Azure Portal a v nabídce vyberte **Analýza nákladů**. Přejděte například na **Předplatná**, vyberte předplatné ze seznamu a pak v nabídce vyberte **Analýza nákladů**. V horní části stránky Analýza nákladů vyberte **Nastavení** a pak **Exporty**.

> [!NOTE]
> - Kromě předplatných můžete vytvářet exporty u skupin prostředků, skupin pro správu, oddělení a registrací. Další informace o rozsazích najdete v tématu [Vysvětlení a práce s rozsahy](understand-work-scopes.md).
>- Pokud jste přihlášeni jako partner v oboru fakturačního účtu nebo k tenantovi zákazníka, můžete exportovat data do účtu Azure Storage, který je propojený s partnerským účtem úložiště. Musíte mít ale aktivní předplatné ve svém tenantovi CSP.

1. Vyberte **Přidat** a zadejte název exportu.
1. Jako **metriku** vyberte:
    - **Skutečné náklady (využití a nákupy)** – Tuto možnost vyberte, pokud chcete exportovat standardní využití a nákupy.
    - **Amortizované náklady (využití a nákupy)** – Tuto možnost vyberte, pokud chcete exportovat amortizované náklady pro nákupy, jako jsou rezervace Azure.
1. V poli **Typ exportu** vyberte:
    - **Denní export nákladů od začátku měsíce** – Poskytuje každý den nový soubor exportu s náklady od začátku měsíce. Nejnovější data jsou agregována z předchozích denních exportů.
    - **Týdenní export nákladů za posledních 7 dní** – Vytvoří týdenní export nákladů za posledních 7 dní od vybraného počátečního data exportu.
    - **Měsíční export nákladů za poslední měsíc** – Poskytne vám export nákladů za poslední měsíc na základě aktuálního měsíce, ve kterém jste export vytvořili. Podle rozvrhu se export spustí vždy pátého dne v měsíci pro náklady za předchozí měsíc.
    - **Jednorázový export** – Umožňuje zvolit časový rozsah historických dat pro export do služby Azure Blob Storage. Můžete exportovat maximálně 90 dní historických nákladů, a to od libovolného data. Tento export se spustí okamžitě a v účtu úložiště je k dispozici do dvou hodin.
        V závislosti na typu exportu buď vyberte počáteční datum, nebo vyberte data **Od** a **Do**.
1. Zadejte předplatné pro účet úložiště Azure a potom vyberte skupinu prostředků nebo vytvořte novou.
1. Vyberte název účtu úložiště nebo vytvořte nový.
1. Vyberte umístění (oblast Azure).
1. Zadejte kontejner úložiště a cestu k adresáři, do kterého chcete soubor exportu poslat.
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="Příklad nového exportu" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. Zkontrolujte podrobnosti o exportu a vyberte **Vytvořit**.

Nový export se zobrazí v seznamu exportů. Ve výchozím nastavení jsou nové exporty povolené. Pokud chcete zakázat nebo odstranit naplánovaný export, vyberte libovolnou položku v seznamu a pak vyberte **Zakázat** nebo **Odstranit**.

Na začátku může trvat 12 až 24 hodin, než se export spustí. Může však trvat i déle, než se v exportovaných souborech zobrazí data.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Začněte přípravou prostředí pro rozhraní příkazového řádku Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. K zobrazení vašich aktuálních exportů po přihlášení použijte příkaz [az costmanagement export list](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_list):

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* Kromě předplatných můžete vytvářet exporty pro skupiny prostředků a skupiny pro správu. Další informace o rozsazích najdete v tématu [Vysvětlení a práce s rozsahy](understand-work-scopes.md).
   >* Pokud jste přihlášeni jako partner v oboru fakturačního účtu nebo k tenantovi zákazníka, můžete exportovat data do účtu Azure Storage, který je propojený s partnerským účtem úložiště. Musíte mít ale aktivní předplatné ve svém tenantovi CSP.

1. Vytvořte skupinu prostředků nebo použijte stávající skupinu prostředků. Skupinu prostředků vytvoříte pomocí příkazu [az group create](/cli/azure/group#az_group_create).

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Vytvořte účet úložiště pro příjem exportů, nebo použijte už existující účet úložiště. Účet úložiště vytvoříte příkazem [az storage account create](/cli/azure/storage/account#az_storage_account_create).

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Pro vytvoření exportu spusťte příkaz [az costmanagement export create](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_create):

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   Jako parametr **--type** můžete zvolit `ActualCost`, `AmortizedCost` nebo `Usage`.

   Tento příklad používá `MonthToDate`. Každý den se vytvoří soubor exportu pro náklady od začátku měsíce. Nejnovější data jsou agregována z předchozích denních exportů tohoto měsíce.

1. K zobrazení podrobných informací o operaci exportu použijte příkaz [az costmanagement export show](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_show):

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. K aktualizaci exportu můžete použít příkaz [az costmanagement export update](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_update):

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   Tento příklad změní výstupní adresář.

>[!NOTE]
>Na začátku může trvat 12 až 24 hodin, než se export spustí. Může však trvat i déle, než se v exportovaných souborech zobrazí data.

K odstranění exportu můžete použít příkaz [az costmanagement export delete](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_delete):

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Začněte přípravou vašeho prostředí pro Azure PowerShell:

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Když je powershellový modul **Az.CostManagement** ve verzi Preview, musíte ho nainstalovat samostatně pomocí rutiny `Install-Module`. Jakmile bude tento powershellový modul obecně dostupný, stane se součástí budoucích verzí modulu Az PowerShellu a bude ve výchozím nastavení dostupný v rámci Azure Cloud Shellu.

  ```azurepowershell-interactive
  Install-Module -Name Az.CostManagement
  ```

1. K zobrazení vašich aktuálních exportů po přihlášení použijte rutinu [Get-AzCostManagementExport](/powershell/module/Az.CostManagement/get-azcostmanagementexport):

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

   >[!NOTE]
   >
   >* Kromě předplatných můžete vytvářet exporty pro skupiny prostředků a skupiny pro správu. Další informace o rozsazích najdete v tématu [Vysvětlení a práce s rozsahy](understand-work-scopes.md).
   >* Pokud jste přihlášeni jako partner v oboru fakturačního účtu nebo k tenantovi zákazníka, můžete exportovat data do účtu Azure Storage, který je propojený s partnerským účtem úložiště. Musíte mít ale aktivní předplatné ve svém tenantovi CSP.

1. Vytvořte skupinu prostředků nebo použijte stávající skupinu prostředků. K vytvoření skupiny prostředků použijte rutinu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

   ```azurepowershell-interactive
   New-AzResourceGroup -Name TreyNetwork -Location eastus
   ```

1. Vytvořte účet úložiště pro příjem exportů, nebo použijte už existující účet úložiště. K vytvoření účtu úložiště použijte rutinu [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount):

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName TreyNetwork -AccountName cmdemo -SkuName Standard_RAGRS -Location eastus
   ```

1. Spusťte rutinu [New-AzCostManagementExport](/powershell/module/Az.CostManagement/new-azcostmanagementexport) pro vytvoření exportu:

   ```azurepowershell-interactive
   $Params = @{
     Name = 'DemoExport'
     DefinitionType = 'ActualCost'
     Scope = 'subscriptions/00000000-0000-0000-0000-000000000000'
     DestinationResourceId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/treynetwork/providers/Microsoft.Storage/storageAccounts/cmdemo'
     DestinationContainer = 'democontainer'
     DefinitionTimeframe = 'MonthToDate'
     ScheduleRecurrence = 'Daily'
     RecurrencePeriodFrom = '2020-06-01T00:00:00Z'
     RecurrencePeriodTo = '2020-10-31T00:00:00Z'
     ScheduleStatus = 'Active'
     DestinationRootFolderPath = 'demodirectory'
     Format = 'Csv'
   }
   New-AzCostManagementExport @Params
   ```

   Jako parametr **DefinitionType** můžete zvolit `ActualCost`, `AmortizedCost` nebo `Usage`.

   Tento příklad používá `MonthToDate`. Každý den se vytvoří soubor exportu pro náklady od začátku měsíce. Nejnovější data jsou agregována z předchozích denních exportů tohoto měsíce.

1. K zobrazení podrobných informací o operaci exportu použijte rutinu `Get-AzCostManagementExport`:

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

1. K aktualizaci exportu můžete použít rutinu [Update-AzCostManagementExport](/powershell/module/Az.CostManagement/update-azcostmanagementexport):

   ```azurepowershell-interactive
   Update-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000' -DestinationRootFolderPath demodirectory02
   ```

   Tento příklad změní výstupní adresář.

>[!NOTE]
>Na začátku může trvat 12 až 24 hodin, než se export spustí. Může však trvat i déle, než se v exportovaných souborech zobrazí data.

K odstranění exportu můžete použít rutinu [Remove-AzCostManagementExport](/powershell/module/Az.CostManagement/remove-azcostmanagementexport):

```azurepowershell-interactive
Remove-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
```

---

### <a name="export-schedule"></a>Plán exportu

Naplánované exporty jsou ovlivněny časem a dnem v týdnu, kdy byl export původně vytvořen. Když vytvoříte naplánovaný export, export se bude spouštět se stejnou frekvencí pro každý další výskyt exportu. Například export nákladů od začátku měsíce nastavený na denní frekvenci se bude spouštět denně. Podobně u týdenního exportu bude export probíhat každý týden ve stejný den, ve kterém je naplánován. Přesná doba doručení exportu není zaručena a exportovaná data jsou dostupná během čtyř hodin od spuštění.

Každý export vytvoří nový soubor, takže starší exporty se nepřepíšou.

#### <a name="create-an-export-for-multiple-subscriptions"></a>Vytvoření exportu pro více předplatných

Pokud máte smlouva Enterprise, můžete použít skupinu pro správu k agregaci informací o nákladech na předplatné v jednom kontejneru. Potom pro tuto skupinu pro správu můžete vyexportovat data služby Cost Management.

Export pro skupiny pro správu jiných typů předplatného se nepodporuje.

1. Pokud jste si ještě nevytvořili skupinu pro správu, vytvořte si ji a přiřaďte do ní předplatná.
1. V analýze nákladů jako rozsah nastavte skupinu pro správu a vyberte možnost **Vybrat tuto skupinu pro správu**.
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="Příklad ukazující možnost Vybrat tuto skupinu pro správu" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. Vytvořením exportu v daném rozsahu získáte data služby Cost Management pro předplatná ve skupině pro správu.
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="Příklad ukazující možnost vytvoření nového exportu s rozsahem skupiny pro správu":::

## <a name="verify-that-data-is-collected"></a>Ověřte, že jsou data shromážděná

Pomocí Průzkumníka služby Azure Storage můžete snadno ověřit, že se data služby Cost Management shromažďují, a zobrazit exportovaný soubor CSV.

V seznamu exportu vyberte název účtu úložiště. Na stránce účtu úložiště vyberte Otevřít v Průzkumníkovi. Pokud se zobrazí potvrzovací okno, vyberte **Ano** a otevřete soubor v Průzkumníkovi služby Azure Storage.

![Stránka účtu úložiště znázorňující ukázkové informace a odkaz Otevřít v Průzkumníkovi](./media/tutorial-export-acm-data/storage-account-page.png)

V Průzkumníkovi služby Storage přejděte ke kontejneru, který chcete otevřít, a vyberte složku odpovídající aktuálnímu měsíci. Zobrazí se seznam souborů CSV. Vyberte jeden soubor a pak vyberte **Otevřít**.

![Ukázkové informace zobrazené v Průzkumníku služby Storage](./media/tutorial-export-acm-data/storage-explorer.png)

Soubor se otevře v programu nebo aplikaci, která je nastavená k otevírání souborů s příponou CSV. Tady je příklad v Excelu.

![Příklad exportovaných dat CSV zobrazených v Excelu](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Stažení vyexportovaného datového souboru CSV

Vyexportovaný soubor CSV si také můžete stáhnout na webu Azure Portal. Následující kroky vysvětlují, jak ho v analýze nákladů najít.

1. V analýze nákladů vyberte **Nastavení** a pak vyberte **Exporty**.
1. V seznamu exportů vyberte účet úložiště pro export.
1. V účtu úložiště vyberte **Kontejnery**.
1. V seznamu kontejnerů vyberte požadovaný kontejner.
1. Projděte adresáře a objekty blob úložiště k požadovanému datu.
1. Vyberte soubor CSV a potom vyberte **Stáhnout**.

[![Příklad stažení exportu](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>Zobrazení historie spuštění exportu

Historii spuštění naplánovaného exportu můžete zobrazit výběrem příslušného exportu na stránce se seznam exportů. Stránka se seznamem exportů také poskytuje rychlý přístup k zobrazení doby běhu předchozích exportů a času příštího spuštění exportu. Tady je příklad ukazující historii spuštění.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="Snímek obrazovky s podoknem Exporty":::

Výběrem exportu zobrazíte jeho historii spuštění.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="Snímek obrazovky s historií spuštění exportu":::

## <a name="access-exported-data-from-other-systems"></a>Přístup k exportovaným datům z jiných systémů

Jedním z důvodů pro export dat služby Cost Management je umožnění přístupu k těmto datům z externích systémů. Možná používáte systém řídicích panelů nebo jiný finanční systém. Takové systémy se dost liší, takže ukázka s příkladem by nemusela být užitečná.  S přístupem k datům z vašich aplikací ale můžete začít podle tématu [Seznámení se službou Azure Storage](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit každodenní export
> * Ověřit shromáždění dat

Přejděte k dalšímu kurzu, který se zabývá optimalizací a zvyšováním efektivity pomocí identifikace nečinných a nedostatečně využitých prostředků.

> [!div class="nextstepaction"]
> [Kontrola optimalizačních doporučení a reakce na ně](tutorial-acm-opt-recommendations.md)
