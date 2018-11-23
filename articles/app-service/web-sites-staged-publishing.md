---
title: Nastavení přípravných prostředí pro web apps ve službě Azure App Service | Dokumentace Microsoftu
description: Naučte se používat fázované publikování webových aplikací ve službě Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
ms.openlocfilehash: 47b67a6a3475b7f159a14825b168bbf0049db9b8
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291143"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Nastavení přípravných prostředí ve službě Azure App Service
<a name="Overview"></a>

Když nasadíte webovou aplikaci, webové aplikace v Linuxu, mobilních back-endu a aplikace API k [služby App Service](https://go.microsoft.com/fwlink/?LinkId=529714), můžete nasadit do samostatného nasazení slotu místo produkčního slotu výchozí při spuštění v **standardní**, **Premium**, nebo **izolované** úroveň plánu služby App Service. Sloty nasazení jsou ve skutečnosti živé aplikace s vlastními názvy hostitele. Elementy obsahu a konfigurace aplikace je možné Prohodit mezi dvěma sloty nasazení, včetně produkčního slotu. Nasazení aplikace do slotu nasazení má následující výhody:

* Před přepnutím pomocí produkčního slotu můžete ověřit změny aplikace do přípravného slotu nasazení.
* Nasazení aplikace do slotu nejprve a přepnutím do produkčního prostředí mít jistotu, že všechny instance slotu zahřejí před přepnutím do produkčního prostředí. Tím se eliminuje prostoje při nasazení vaší aplikace. Přesměrování provozu je bezproblémové a jako výsledek operace prohození se zahodí žádné žádosti. Tento celý pracovní postup je možné automatizovat pomocí konfigurace [automatického prohození](#Auto-Swap) když není potřeba prohození předběžné ověření.
* Po převodu se slot s dříve připravenou aplikace teď má předchozí produkční aplikace. Pokud se změny přepnutím do produkčního slotu není podle očekávání, můžete provést stejný prohození okamžitě zobrazíte "poslední známé dobré webu" zpět.

Každá úroveň plánu služby App Service podporuje jiný počet nasazovacích slotů. Chcete zjistit počet slotů vaší aplikace úroveň podporuje, najdete v článku [omezení služby App](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Umožní škálování vaší aplikace do jiné úrovně, musí podporovat na cílové úrovni počtu slotů, které už aplikace používá. Například pokud vaše aplikace obsahuje více než 5. sloty, nelze ji převedete na **standardní** vrstvy, protože **standardní** úroveň podporuje jenom 5 sloty nasazení.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Přidávání slotu nasazení
Aplikace musí být spuštěn v **standardní**, **Premium**, nebo **izolované* úrovně měli povolit několik nasazovacích slotů.

1. V [webu Azure Portal](https://portal.azure.com/), otevřete v této aplikaci [okno prostředku](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Zvolte **sloty nasazení** možnost a potom klikněte na **přidat Slot**.
   
    ![Přidat nový slot nasazení][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Pokud aplikace ještě není v **standardní**, **Premium**, nebo **izolované* vrstvy, obdržíte zprávu s oznámením podporované úrovně pro povolení fázované publikování. V tomto okamžiku máte možnost vybrat si **upgradovat** a přejděte do **škálování** kartu vaší aplikace, než budete pokračovat.
   > 
   > 
3. V **přidat slot** okně zadejte název slotu a vyberte, jestli se má klonovat konfigurace aplikace z jiného existujícího slotu nasazení. Klikněte na zaškrtávací políčko, abyste mohli pokračovat.
   
    ![Zdroj konfigurace][ConfigurationSource1]
   
    Při prvním přidání slot, máte dvě možnosti: klonování konfiguraci z výchozí pozici v produkčním prostředí nebo vůbec ne.
    Po vytvoření několika sloty, bude možné klonovat konfiguraci slotu než v produkčním prostředí:
   
    ![Konfigurace zdrojů][MultipleConfigurationSources]
4. V okně prostředků vaší aplikace, klikněte na tlačítko **sloty nasazení**, klikněte na slot nasazení otevřete okno prostředků tohoto slotu, sadu metriky a konfigurace stejně jako jakoukoli jinou aplikaci. Název slotu zobrazuje v horní části okna vás upozorní, že si prohlížíte slotu nasazení.
   
    ![Název slotu nasazení][StagingTitle]
5. Klikněte na adresu URL aplikace v okně slot. Všimněte si, že slot nasazení má svůj vlastní název hostitele a je také živé aplikace. Omezit veřejný přístup k slot pro nasazení, naleznete v tématu [webové aplikaci App Service – web blokovat přístup k nasazení o neprodukční sloty](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Po vytvoření slotu nasazení není žádný obsah. Můžete nasadit na pozici v jiném úložišti větev nebo úplně jiném úložišti. Můžete také změnit konfiguraci slotu. Použijte Publikovat profil nebo nasazení přihlašovací údaje související s slot nasazení pro aktualizace obsahu.  Například můžete [publikovat tento slot s gitem](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Nastavení, které jsou přehozeny?
Při klonování konfiguraci z jiného slot nasazení klonovaného konfigurace je upravovat. Kromě toho některé konfigurační prvky bude následovat obsahu napříč odkládacího souboru (ne slot konkrétní) zatímco ostatní prvky konfigurace zůstanou na stejné pozici po prohození (slot konkrétní). Následující seznamy shrnují nastavení, které se mění při prohození slotů.

**Nastavení, která jsou přehozeny**:

* Obecné nastavení – třeba framework verze, 32 nebo 64-bit, Web sockets
* Nastavení aplikace (lze nakonfigurovat pro zůstanou slotu)
* Připojovací řetězce (může být nakonfigurovaný zůstanou slotu)
* Mapování obslužných rutin
* Nastavení monitorování a diagnostiky
* Obsah WebJobs
* Hybridní připojení

**Nastavení, která nejsou Prohodit**:

* Publikování koncových bodů
* Vlastní názvy domén
* Certifikáty SSL a vazeb
* Nastavení škálování
* WebJobs plánovače

Konfigurace aplikaci nastavení nebo připojovací řetězec k zůstanou slotu (ne Prohodit), přístup **nastavení aplikace** okno pro konkrétní datovou oblast, vyberte **nastavení slotu** pole pro konfiguraci elementy, které by měla zůstat na slot. Označení prvek konfigurace slotu konkrétní nemá vliv zřízení tento prvek jako není swappable přes všechny sloty nasazení přidružené aplikace.

![Nastavení slotu][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>Prohození slotů nasazení 
Můžete prohození slotů nasazení v **přehled** nebo **sloty nasazení** zobrazit okno prostředků vaší aplikace.

> [!IMPORTANT]
> Před přepnutí aplikace z slot nasazení do produkčního prostředí, ujistěte se, že všechna nastavení konkrétní bez slot jsou nakonfigurované přesně tak, jak chcete mít v cíli prohození.
> 
> 

1. Prohození slotů nasazení, klikněte na tlačítko **prohození** tlačítko na panelu příkazů aplikace nebo na panelu příkazů slotu nasazení.
   
    ![Tlačítko Prohození][SwapButtonBar]

2. Ujistěte se, že jsou správně nastavena prohození zdroj a cíl prohození. Cíl odkládacího souboru je obvykle produkční slot. Klikněte na tlačítko **OK** k dokončení operace. Po ukončení operace mají se Prohodit sloty nasazení.

    ![Dokončit prohození](./media/web-sites-staged-publishing/SwapImmediately.png)

    Pro **prohození s náhledem** typ prohození, přečtěte si téma [prohození s náhledem (vícefázových swap)](#Multi-Phase).  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>Prohození s náhledem (vícefázových swap)

Prohození s náhledem nebo více fáze prohození zjednodušit ověření konfigurace pro slot konkrétní prvky, jako je například připojovací řetězce.
Pro klíčové úlohy, kterou chcete ověřit, které se aplikace chová podle očekávání při použití konfigurace slotu produkční a musíte provést tyto ověření *před* aplikace je přepnutím do produkčního prostředí. Prohození s náhledem je, co potřebujete.

> [!NOTE]
> Prohození s náhledem není podporované ve službě web apps v Linuxu.

Při použití **prohození s náhledem** možnost (viz [prohození slotů nasazení](#Swap)), služby App Service provede následující:

- Udržuje kapacita cílového slotu beze změny, neovlivní existující úlohy na této pozici (například produkčního).
- Použije konfigurační prvky kapacita cílového slotu na zdrojový slot, včetně specifické pro slot připojovací řetězce a nastavení aplikace.
- Restartování pracovních procesů na zdrojový slot pomocí těchto výše uvedené konfigurační prvky.
- Po dokončení prohození: přesune do kapacita cílového slotu předprodukční warmed nahoru zdrojový slot. Cílový slot se přesune na zdrojový slot jako ruční prohození.
- Když zrušíte prohození: znovu použije konfigurace prvky zdrojový slot na zdrojový slot.

Ve verzi preview přesně, jak se aplikace chovat se kapacita cílového slotu konfigurací. Po dokončení ověření dokončit velikost odkládacího souboru v samostatný krok. Tento krok má další výhodu v tom, které zdrojový slot je už provozní teplotu s požadovanou konfigurací a není klientů docházet k žádné výpadky.  

Rutiny Azure Powershellu pro oddíl sloty nasazení jsou součástí ukázek pro rutiny Azure Powershellu, který je k dispozici na několika fáze prohození.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurovat automatické prohození
Automatické prohození zjednodušuje scénáře DevOps, ve které chcete pro koncové zákazníky aplikace průběžně nasazení vaší aplikace s nulovou úplné spuštění a bez výpadků. Když slot nasazení je nakonfigurována pro automatické prohození do produkčního prostředí, pokaždé, když vložíte změny do tohoto slotu aktualizace vašeho kódu, App Service automaticky přepnutí aplikace do produkčního prostředí po jeho má již provozní teplotu ve slotu.

> [!IMPORTANT]
> Když povolíte automatické prohození slotu, ujistěte se, že konfigurace slotu přesně konfigurace určené pro cílový slot (obvykle produkčního slotu).
> 
> 

> [!NOTE]
> Automatické prohození se nepodporuje ve službě web apps v Linuxu.

Konfigurace automatického prohození pro slot je snadné. Postupujte následovně:

1. V **sloty nasazení**, vyberte jiné produkčního slotu a zvolte **nastavení aplikace** v okně prostředků tohoto slotu.  
   
    ![][Autoswap1]
2. Vyberte **na** pro **automatického prohození**, vyberte požadovaný cílový slot v **automatického prohození slotu**a klikněte na tlačítko **Uložit** na panelu příkazů. Ujistěte se, že konfigurace pro slot právě konfigurace určené pro cílový slot.
   
    **Oznámení** kartu bliká zelený **úspěch** po dokončení operace.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > K otestování automatického prohození pro vaši aplikaci, vyberte nejprve neprodukční cílovém slotu v **automatického prohození slotu** abyste se seznámili s funkcí.  
   > 
   > 
3. Spusťte kód push do tohoto slotu nasazení. Automatické prohození se stane po krátkou dobu a aktualizace se projeví na adrese URL cílovém slotu.

<a name="Rollback"></a>

## <a name="roll-back-a-production-app-after-swap"></a>Vrátit zpět produkční aplikace po prohození
Pokud všechny chyby identifikované v produkčním prostředí po prohození slotů, vrátit slotů zpět do stavu před prohození pomocí stejné dvěma sloty výměny okamžitě.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Vlastní zahřívání před prohození
Některé aplikace mohou vyžadovat akce vlastní zahřívání. `applicationInitialization` Konfiguračního prvku v souboru web.config můžete zadat vlastní inicializaci akce má být provedena před přijetí požadavku. Operace prohození počká tento vlastní zahřívání dokončete. Tady je ukázka fragmentu souboru web.config.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostname="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

## <a name="monitor-swap-progress"></a>Průběh můžete monitorovat prohození

Operace prohození někdy trvá nějakou dobu, například pokud má aplikace, kterou Prohodí zahřívání dlouhou dobu. Můžete získat další informace o operacích odkládacího souboru v [protokolu aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) v [webu Azure portal](https://portal.azure.com).

Na stránce vaší aplikace z portálu, v levém navigačním panelu vyberte **protokolu aktivit**.

Operace prohození se objeví v protokolu dotazu jako `Slotsswap`. Můžete ji rozbalte a vyberte jeden z dílčích operací nebo chyby, pokud chcete zobrazit podrobnosti.

![Protokol aktivit pro prohození slotů](media/web-sites-staged-publishing/activity-log.png)

<a name="Delete"></a>

## <a name="delete-a-deployment-slot"></a>Odstranění slotu nasazení
V okně pro slot nasazení, otevře se okno slot nasazení, klikněte na tlačítko **přehled** (výchozí stránka) a klikněte na tlačítko **odstranit** na panelu příkazů.  

![Odstranění slotu nasazení][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-azure-powershell"></a>Automatizace pomocí Azure Powershellu

Prostředí Azure PowerShell je modul, který obsahuje rutiny pro správu Azure pomocí Windows Powershellu, včetně podpory ke správě slotů nasazení v Azure App Service.

* Informace o instalaci a konfiguraci prostředí Azure PowerShell a na ověřování prostředí Azure PowerShell ve vašem předplatném Azure, najdete v části [instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-a-web-app"></a>Vytvoření webové aplikace
```PowerShell
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Vytvoří slot nasazení
```PowerShell
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Zahájení prohození s náhledem (vícefázových swap) a použít konfigurace cílového slotu na zdrojový slot
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Zrušit čekající prohození (prohození s revizí) a obnovení konfigurace zdrojový slot
```PowerShell
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Prohození slotů nasazení
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorování odkládacího souboru událostí v protokolu aktivit
```PowerShell
Get-AzureRmLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-deployment-slot"></a>Odstranit slot pro nasazení
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-azure-cli"></a>Automatizace pomocí Azure CLI

Pro [rozhraní příkazového řádku Azure](https://github.com/Azure/azure-cli) příkazy pro sloty nasazení, najdete v článku [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>Další postup
[Azure App Service Web aplikace – webové blokovat přístup k nasazení o neprodukční sloty](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)  
[Úvod do služby App Service v Linuxu](../app-service/containers/app-service-linux-intro.md)  
[Bezplatná zkušební verze Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png

