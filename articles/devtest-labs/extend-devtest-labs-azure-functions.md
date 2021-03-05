---
title: Rozšíří Azure DevTest Labs pomocí Azure Functions | Microsoft Docs
description: Naučte se, jak můžete Azure DevTest Labs pomocí Azure Functions roztáhnout.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7a51f0935540df18cfb8805902bbe2c4ec365291
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203670"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Rozšíření DevTest Labs s využitím služby Azure Functions
Azure Functions můžete použít k podpoře dalších scénářů nad rámec těch, které už podporuje DevTest Labs. Azure Functions můžete použít k rozšiřování integrovaných funkcí služby, aby splňovaly potřeby vaší firmy. Následující seznam uvádí některé z možných scénářů. V tomto článku se dozvíte, jak implementovat jeden z těchto ukázkových scénářů.

- Zadejte souhrn virtuálních počítačů v testovacím prostředí na nejvyšší úrovni.
- [Konfigurace testovacího prostředí pro použití brány vzdálené plochy](configure-lab-remote-desktop-gateway.md)
- Vytváření sestav dodržování předpisů na stránce interní podpory
- Umožněte uživatelům dokončit operace, které vyžadují zvýšená oprávnění v předplatném.
- [Spouštění pracovních postupů na základě událostí DevTest Labs](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Přehled
[Azure Functions](../azure-functions/functions-overview.md) je platforma pro výpočetní prostředí bez serveru v Azure. Použití Azure Functions v řešení s DevTest Labs nám umožňuje rozšířit stávající funkce vlastním kódem. Další informace o Azure Functions najdete v [dokumentaci k Azure Functions](../azure-functions/functions-overview.md). V tomto článku se dozvíte, jak Azure Functions může pomáhat s splněním požadavků nebo kompletních scénářů v DevTest Labs. Tento článek obsahuje příklad poskytnutí souhrnu virtuálních počítačů v testovacím prostředí na nejvyšší úrovni, jak je znázorněno níže:

**Příklad požadavku/scénáře**: uživatelé můžou zobrazit podrobnosti o všech virtuálních počítačích v testovacím prostředí, včetně operačního systému, vlastníka a všech použitých artefaktů.  Pokud se navíc v poslední době nepoužil artefakt **Apply Updates** , existuje jednoduchý způsob, jak ho použít.

K dokončení tohoto scénáře budete používat dvě funkce, jak je popsáno v následujícím diagramu:  

![Celkový tok](./media/extend-devtest-labs-azure-functions/flow.png)

Zdrojový kód těchto ukázkových funkcí se nachází v [úložišti GitHub DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (jsou k dispozici obě implementace C# i PowerShellu).

- **UpdateInternalSupportPage**: Tato funkce se dotazuje na DevTest Labs a aktualizuje stránku interní podpory přímo s podrobnostmi o virtuálních počítačích.
- **ApplyWindowsUpdateArtifact**: pro virtuální počítač v testovacím prostředí Tato funkce používá artefakt služby **Windows Update** .

## <a name="how-it-works"></a>Jak to funguje
Když uživatelé na DevTest Labs vyberou stránku **interní podpory** , mají předem vyplněnou stránku s informacemi o virtuálních počítačích, vlastníkech testovacího prostředí a kontaktech podpory.  

Když vyberete tlačítko **Vybrat zde k aktualizaci** , stránka volá první funkci Azure: **UpdateInternalSupportPage**. Funkce se dotazuje na DevTest Labs na informace a potom přepíše stránku **interní podpory** novými informacemi.

K dispozici je další akce, kterou je možné provést pro všechny virtuální počítače, na kterých se v poslední době nepoužily web Windows Update artefakty. na virtuálním počítači se pak zobrazí tlačítko pro použití aktualizací Windows. Když vyberete pro virtuální počítač tlačítko ***spustit Windows Update** , stránka volá druhou funkci Azure: **ApplyWindowsUpdateArtifact**. Tato funkce zkontroluje, jestli je virtuální počítač spuštěný, a pokud ano, aplikuje artefakt [web Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) přímo.

## <a name="step-by-step-walkthrough"></a>Podrobný návod
V této části najdete podrobné pokyny k nastavení prostředků Azure potřebných k aktualizaci stránky **interní podpory** . Tento návod poskytuje jeden příklad rozšíření DevTest Labs. Tento model můžete použít pro jiné scénáře.

### <a name="step-1-create-a-service-principal"></a>Krok 1: Vytvoření instančního objektu 
Prvním krokem je získání instančního objektu s oprávněním k předplatnému, které obsahuje testovací prostředí. Instanční objekt musí používat ověřování na základě hesla. Můžete to udělat pomocí [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli), [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)nebo [Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md). Pokud již používáte instanční objekt, můžete tento krok přeskočit.

Poznamenejte si **ID aplikace**, **klíč** a **ID tenanta** objektu služby. Budete je potřebovat později v tomto návodu. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Krok 2: Stáhněte si ukázku a otevřete ji v aplikaci Visual Studio 2019
Stáhněte si kopii [ukázky Azure Functions v C#](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) místně (buď klonováním úložiště, nebo stažením úložiště z [tohoto místa](https://github.com/Azure/azure-devtestlab/archive/master.zip)).  

1. Otevřete ukázkové řešení pomocí sady Visual Studio 2019.  
1. Nainstalujte úlohu **vývoj pro Azure** pro Visual Studio, pokud ji ještě nemáte nainstalovanou. Dá se nainstalovat prostřednictvím **nástrojů**  ->  **získat nástroje a** položku nabídky funkce).

    ![Úlohy vývoje Azure](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Sestavte řešení. Vyberte **sestavení** a pak položku nabídky **řešení** .

### <a name="step-3-deploy-the-sample-to-azure"></a>Krok 3: nasazení ukázky do Azure
V aplikaci Visual Studio v okně **Průzkumník řešení** klikněte pravým tlačítkem na projekt **AzureFunctions** a vyberte **publikovat**. Postupujte podle pokynů průvodce a dokončete publikování do nového nebo stávajícího Function App Azure. Podrobné informace o vývoji a nasazení služby Azure Functions pomocí sady Visual Studio naleznete v tématu [vývoj Azure Functions pomocí sady Visual Studio](../azure-functions/functions-develop-vs.md).

![Dialog publikovat](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Krok 4: shromáždění nastavení aplikace
Po publikování funkcí je třeba získat adresy URL pro tyto funkce z Azure Portal. 

1. Přejděte na [Azure Portal](https://portal.azure.com). 
1. Najděte aplikaci Function App.
1. Na stránce **aplikace Function** App vyberte funkci. 
1. Vyberte **získat adresu URL funkce** , jak je znázorněno na následujícím obrázku. 

    ![Adresy URL služby Azure Functions](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Zkopírujte a uložte adresu URL. Opakujte tyto kroky pro druhou funkci Azure Functions. 

Také budete potřebovat další informace o instančním objektu, jako je ID aplikace, klíč a ID tenanta.


### <a name="step-5--update-application-settings"></a>Krok 5: aktualizace nastavení aplikace
V aplikaci Visual Studio po publikování funkce Azure vyberte v části **Akce** možnost **Upravit Azure App Service nastavení** . Aktualizujte následující nastavení aplikace (vzdálené):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (výchozí až 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Nastavení aplikace](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Krok 6: testování funkce Azure Functions
Posledním krokem v tomto návodu je testování funkce Azure Functions.  

1. V aplikaci Function App vytvořené v kroku 3 přejděte na funkci **UpdateInternalSupportPage** . 
1. Na pravé straně stránky vyberte **test** . 
1. Zadejte ve vlastnostech trasy (LABNAME, RESOURCEGROUPNAME a SUBSCRIPTIONID).
1. Pokud chcete funkci spustit, vyberte **Spustit** .  

    Tato funkce aktualizuje stránku interní podpory v zadaném testovacím prostředí. Obsahuje také tlačítko pro uživatele, kteří můžou funkci příště volat hned.

    ![Test – funkce](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Další kroky
Azure Functions může přispět k rozšiřování funkcí DevTest Labs nad rámec toho, co už je integrované, a pomáhat zákazníkům, aby si vyplnili své jedinečné požadavky na své týmy. Tento model se dá rozšířit & další, aby se kryl ještě víc.  Další informace o DevTest Labs najdete v následujících článcích: 

- [Referenční architektura DevTest Labs Enterprise](devtest-lab-reference-architecture.md)
- [Nejčastější dotazy](devtest-lab-faq.md)
- [Škálování DevTest Labs](devtest-lab-guidance-scale.md)
- [Automatizace DevTest Labs pomocí PowerShellu](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








