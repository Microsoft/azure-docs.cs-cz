---
title: Rozšíření laboratoří Azure DevTest pomocí funkcí Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak rozšířit Azure DevTest Labs pomocí Azure Functions.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70014357"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Rozšíření DevTest Labs s využitím služby Azure Functions
Funkce Azure můžete použít k podpoře dalších scénářů nad rámec ty, které jsou již podporovány DevTest Labs. Funkce Azure lze použít k rozšíření integrované funkce služby tak, aby vyhovovaly vašim specifickým potřebám. Následující seznam obsahuje některé z možných scénářů. Tento článek ukazuje, jak implementovat jeden z těchto ukázkových scénářů.

- Poskytněte souhrn virtuálních počítačů (VM) nejvyšší úrovně v testovacím prostředí
- [Konfigurace testovacího prostředí pro použití brány vzdálené plochy](configure-lab-remote-desktop-gateway.md)
- Hlášení o dodržování předpisů na stránce interní podpory
- Umožněte uživatelům dokončit operace, které vyžadují zvýšená oprávnění v předplatném.
- [Spuštění pracovních postupů na základě událostí DevTest Labs](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Přehled
[Azure Functions](../azure-functions/functions-overview.md) je výpočetní platforma bez serveru v Azure. Použití funkce Azure v řešení s DevTest Labs nám umožňuje rozšířit stávající funkce s naší vlastní kód. Další informace o funkcích Azure najdete v [dokumentaci k funkcím Azure](../azure-functions/functions-overview.md). Chcete-li ilustrovat, jak funkce Azure může pomoci splnit vaše požadavky nebo dokončit scénáře v DevTest Labs, tento článek používá příklad poskytování nejvyšší úrovně souhrn virtuálních počítačů v testovacím prostředí takto:

**Příklad požadavku/scénáře:** Uživatelé mohou zobrazit podrobnosti o všech virtuálních počítačů v testovacím prostředí, včetně operačního systému, vlastníka a všechny použité artefakty.  Kromě toho pokud **artefakt Apply Windows Updates** nebyl nedávno použit, existuje snadný způsob, jak ho použít.

K dokončení scénáře použijete dvě funkce popsané v následujícím diagramu:  

![Celkový průtok](./media/extend-devtest-labs-azure-functions/flow.png)

Zdrojový kód pro tyto ukázkové funkce je umístěn v [úložišti GitHub DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (implementace C# i PowerShell jsou k dispozici).

- **UpdateInternalSupportPage**: Tato funkce dotazuje DevTest Labs a aktualizuje interní stránku podpory přímo s podrobnostmi o virtuálních počítačích.
- **ApplyWindowsUpdateArtifact**: Pro virtuální ho virtuálního mě v testovacím prostředí tato funkce použije artefakt **aktualizace systému Windows.**

## <a name="how-it-works"></a>Jak to funguje
Když uživatelé vyberte stránku **Interní podpora** v DevTest Labs, mají předem vyplněnou stránku s informacemi o virtuálních počítačůch, majitelích testovacího prostředí a kontaktech podpory.  

Když vyberete tlačítko **Vybrat zde k aktualizaci,** stránka zavolá první funkci Azure: **UpdateInternalSupportPage**. Funkce dotazuje DevTest Labs pro informace a potom přepíše **vnitřní podporu** stránku s novými informacemi.

Existuje další akce, kterou lze podniknout, pro všechny virtuální servery, na kterých nebyly nedávno použity artefakty služby Windows Update, bude k dispozici tlačítko pro použití aktualizací systému Windows pro virtuální počítač. Když vyberete * Spustit tlačítko**aktualizace systému Windows** pro virtuální počítač, stránka volá druhou funkci Azure: **ApplyWindowsUpdateArtifact**. Tato funkce zkontroluje, zda je virtuální počítač spuštěn, a pokud ano, použije artefakt [služby Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) přímo.

## <a name="step-by-step-walkthrough"></a>Podrobný návod
Tato část obsahuje podrobné pokyny pro nastavení prostředků Azure potřebných k aktualizaci stránky **interní podpory.** Tento návod poskytuje jeden příklad rozšíření DevTest Labs. Tento vzor můžete použít pro jiné scénáře.

### <a name="step-1-create-a-service-principal"></a>Krok 1: Vytvoření instančního objektu 
Prvním krokem je získat instanční objekt s oprávněním k předplatnému, které obsahuje testovací prostředí. Instanční objekt musí používat ověřování založené na heslech. Dá se to udělat pomocí [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest), [Azure PowerShellu](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)nebo [portálu Azure](../active-directory/develop/howto-create-service-principal-portal.md). Pokud již máte instanční objekt, který chcete použít, můžete tento krok přeskočit.

Poznamenejte si **ID aplikace**, **klíč**a **ID klienta** pro instanční objekt. Budete je potřebovat později v tomto návodu. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Krok 2: Stažení ukázky a otevření v Visual Studiu 2019
Stáhněte si kopii [ukázky C# Azure Functions](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) místně (buď klonováním úložiště nebo stažením úložiště [odtud).](https://github.com/Azure/azure-devtestlab/archive/master.zip)  

1. Otevřete ukázkové řešení s Visual Studio 2019.  
1. Nainstalujte **úlohu vývoje Azure** pro Visual Studio, pokud ho ještě nemáte nainstalované. To může být instalován přes **Nástroje** -> **Získat nástroje a funkce** položky menu).

    ![Pracovní vytížení pro vývoj Azure](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Sestavte řešení. Vyberte **Sestavit** a pak **vytvořit položku** nabídky řešení.

### <a name="step-3-deploy-the-sample-to-azure"></a>Krok 3: Nasazení ukázky do Azure
V Visual Studiu v **okně Průzkumník řešení** klikněte pravým tlačítkem myši na projekt **AzureFunctions** a potom vyberte **Publikovat**. Podle průvodce dokončete publikování na novou nebo existující aplikaci Azure Function App. Podrobné informace o vývoji a nasazování funkcí Azure pomocí Visual Studia najdete v [tématu Vývoj funkcí Azure pomocí Sady Visual Studio](../azure-functions/functions-develop-vs.md).

![Dialogové okno Publikovat](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Krok 4: Shromáždění nastavení aplikace
Po publikování funkcí je potřeba získat adresy URL pro tyto funkce z webu Azure Portal. 

1. Přejděte na [Azure Portal](https://portal.azure.com). 
1. Najděte aplikaci funkcí.
1. Na stránce **Aplikace funkcí** vyberte funkci. 
1. Vyberte **Získat adresu URL funkce,** jak je znázorněno na následujícím obrázku. 

    ![Adresy URL funkcí Azure](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Zkopírujte a uložte adresu URL. Opakujte tyto kroky pro ostatní funkce Azure. 

Budete také potřebovat další informace o instančníobjekt, jako je ID aplikace, klíč a ID klienta.


### <a name="step-5--update-application-settings"></a>Krok 5: Aktualizace nastavení aplikace
Ve Visual Studiu po publikování funkce Azure vyberte **upravit nastavení služby Azure App Service** v části **Akce**. Aktualizace následujících nastavení aplikace (vzdálená):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (výchozí 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Nastavení aplikace](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Krok 6: Testování funkce Azure
Posledním krokem v tomto návodu je otestovat funkci Azure.  

1. Přejděte na funkci **UpdateInternalSupportPage** v aplikaci funkce vytvořené v kroku 3. 
1. Na pravé straně stránky vyberte **Testovat.** 
1. Zadejte vlastnosti postupu (LABNAME, RESOURCEGROUPNAME a SUBSCRIPTIONID).
1. Chcete-li spustit funkci, vyberte možnost **Spustit.**  

    Tato funkce aktualizuje interní stránku podpory zadaného testovacího prostředí. Obsahuje také tlačítko pro uživatele, aby přímo volat funkci příště

    ![Testovací funkce](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Další kroky
Funkce Azure můžou pomoct rozšířit funkce DevTest Labs nad rámec toho, co už je integrované, a pomoct zákazníkům splnit jejich jedinečné požadavky pro jejich týmy. Tento vzor lze rozšířit & dále rozšířit tak, aby pokrýval ještě více.  Další informace o devTest Labs najdete v následujících článcích: 

- [DevTest Labs Enterprise Referenční architektura](devtest-lab-reference-architecture.md)
- [Nejčastější dotazy](devtest-lab-faq.md)
- [Škálování devTest Labs](devtest-lab-guidance-scale.md)
- [Automatizace testovacích prostředí DevTest pomocí prostředí PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








