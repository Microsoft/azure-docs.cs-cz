---
title: Nastavení přípravných prostředí pro webové aplikace v Azure App Service | Microsoft Docs
description: Naučte se používat dvoufázové instalace publikování pro webové aplikace v Azure App Service.
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
ms.openlocfilehash: c02b7a74eea6973d6ccfbc1cc59d15bfd5cb5b77
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Nastavení přípravných prostředí v Azure App Service
<a name="Overview"></a>

Při nasazení vaší webové aplikace, webové aplikace na Linuxu, mobilní back-end a aplikaci API [služby App Service](http://go.microsoft.com/fwlink/?LinkId=529714), můžete nasadit do samostatné nasazovací slot místo výchozí produkční slot při spuštění v **standardní** nebo **Premium** úroveň plánu služby App Service. Nasazovací sloty jsou ve skutečnosti za provozu aplikace s vlastní názvy hostitelů. Můžete si místo aplikace obsahu a konfigurace – elementy mezi dvěma sloty nasazení, včetně produkční slot. Nasazení aplikace na slot nasazení má následující výhody:

* Můžete ověřit změny aplikace v přípravný slot nasazení před odkládací s produkční slot.
* Nasazení aplikace do patice nejprve a odkládací do provozu zajišťuje, že jsou všechny instance přihrádky jestli před prohazují do produkčního prostředí. Tím se eliminuje výpadek při nasazení aplikace. Přesměrování provozu je bezproblémové a jsou v důsledku operace prohození vyřadit žádné požadavky. Tento celý pracovní postup je možné automatizovat tak, že nakonfigurujete [Prohodit automaticky](#Auto-Swap) při swap předběžné ověření není potřeba.
* Po prohození slot s dříve dvoufázové instalace aplikace teď má předchozí produkční aplikaci. Pokud změny, které jsou vzájemně zaměněny na produkční slot není podle očekávání, můžete provést stejný prohození okamžitě k získání "poslední známé funkční web" zpět.

Každá úroveň plánu služby App Service podporuje různé počty nasazovací sloty. Chcete-li zjistit počet přihrádek podporuje vrstvy vaší aplikace naleznete v tématu [omezení služby App](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#app-service-limits).

* Pokud vaše aplikace obsahuje více sloty, nelze změnit úroveň.
* Škálování není k dispozici pro nevýrobní prostředí sloty.
* Správa propojeného prostředku není podporován pro nevýrobní sloty. V [portálu Azure](http://go.microsoft.com/fwlink/?LinkId=529715) pouze se vyhnout této potenciální dopad na produkční slot dočasně přesunutím mimo produkční slot k jiné vrstvě plán služby App Service. Všimněte si, že jiný produkční slot musí znovu sdílet stejné vrstvě s produkční slot předtím, než můžete zaměnit dva sloty.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Přidat slot nasazení
Aplikace musí být spuštěna v **standardní** nebo **Premium** vrstvy v pořadí, můžete povolit více nasazovací sloty.

1. V [portálu Azure](https://portal.azure.com/), otevřete v této aplikaci [okna prostředků](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Vyberte **nasazovací sloty** možnost a potom klikněte na **přidat Slot**.
   
    ![Přidat nový slot nasazení][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Pokud aplikace ještě není v **standardní** nebo **Premium** vrstvy, obdržíte zprávu s upozorněním, podporované úrovně pro povolení publikování dvoufázové instalace. Nyní máte možnost vybrat **Upgrade** a přejděte do **škálování** kartě vaší aplikace, než budete pokračovat.
   > 
   > 
3. V **přidat slot** , pojmenujte přihrádky a vyberte, zda chcete klonovat konfigurace aplikací z jiného existujícího slotu nasazení. Kliknutím na značku zaškrtnutí pokračujte.
   
    ![Zdroj konfigurace][ConfigurationSource1]
   
    Při prvním přidání slotu, budou mít pouze dvě možnosti: klonování konfigurace z výchozí přihrádky v produkčním prostředí nebo vůbec.
    Po vytvoření několik sloty, bude možné klonovat konfiguraci z slotu než v provozním prostředí:
   
    ![Konfigurace zdroje][MultipleConfigurationSources]
4. V okně prostředků aplikace, klikněte na tlačítko **nasazovací sloty**, pak klikněte na tlačítko slotu nasazení tohoto slotu okna prostředků, otevřete sadu metriky a konfigurace stejně jako kterákoli jiná aplikace. Název slotu se zobrazí v horní části okna s upozorněním, že jsou zobrazeny slotu nasazení.
   
    ![Název slotu nasazení][StagingTitle]
5. Klikněte na adresu URL aplikace v okně na slot. Všimněte si slotu nasazení má svůj vlastní název hostitele a také za provozu aplikace. Chcete-li omezit veřejný přístup k slotu nasazení, přečtěte si téma [webové aplikace App Service – blok webového přístupu k testovacím nasazovací sloty](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Po vytvoření slotu nasazení není k dispozici žádný obsah. Můžete nasadit na slot z různých úložiště větev nebo úplně jiné úložiště. Také můžete změnit konfiguraci na slot. Použití profilu nebo nasazení pověření publikovat přidružené slotu nasazení pro aktualizace obsahu.  Například můžete [publikovat do této pozici s gitem](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="configuration-for-deployment-slots"></a>Konfigurace pro nasazovací sloty
Když naklonovat konfiguraci z jiného slotu nasazení klonovaného konfigurace je upravovat. Kromě toho některé konfigurace – elementy bude postupovat podle obsahu mezi prohození (ne slotu konkrétní) při další konfigurace – elementy zůstanou v stejný slot. po prohození (slotu konkrétní). Následující seznamy shrnují konfiguraci, která se změní při Prohodit sloty.

**Nastavení, které jsou vzájemně zaměněny**:

* Obecné nastavení – například framework verze 32/64-bit, webové sokety
* Nastavení aplikace (může být nakonfigurován chtěl slot)
* Připojovací řetězce (může být nakonfigurován chtěl slot)
* Mapování obslužných rutin
* Nastavení monitorování a diagnostiky
* Obsah webové úlohy

**Nastavení, které nejsou vzájemně zaměněny**:

* Publikování koncové body
* Názvy vlastních domén
* Certifikáty SSL a vazeb
* Nastavení škálování
* Webové úlohy plánovače

Konfigurace aplikaci nastavení nebo připojovací řetězec chtěl slot (není vzájemně zaměněny), přístup **nastavení aplikace** okno pro konkrétní pozici, pak vyberte **nastavení slotu** pole pro konfigurační prvky, které by měl přilepit přihrádky. Všimněte si, že označíte konfigurační prvek jako slotu konkrétní má za následek vytvoření tohoto prvku jako není swappable napříč všechny nasazovací sloty přidružené aplikaci.

![Nastavení slotu][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>Prohodit sloty nasazení 
Můžete Prohodit sloty nasazení v **přehled** nebo **nasazovací sloty** zobrazení okna prostředků vaší aplikace.

> [!IMPORTANT]
> Než můžete odkládacího souboru aplikace z slotu nasazení do produkčního prostředí, ujistěte se, že všechna bez slotu konkrétní nastavení jsou nakonfigurována přesně tak, jak budete chtít mít v cílové odkládacího souboru.
> 
> 

1. Pokud chcete Prohodit sloty nasazení, klikněte na tlačítko **Swap** tlačítka na panelu příkazů aplikace nebo na panelu příkazů slot nasazení.
   
    ![Swap – tlačítko][SwapButtonBar]

2. Ujistěte se, že jsou správně nastaveny swap zdrojové a cílové odkládacího souboru. Cíl odkládacího souboru je obvykle produkční slot. Klikněte na tlačítko **OK** k dokončení operace. Po dokončení operace mít byla vzájemně zaměněny nasazovací sloty.

    ![Dokončit prohození](./media/web-sites-staged-publishing/SwapImmediately.png)

    Pro **prohození s náhledem** Prohodit typu, najdete v části [prohození s náhledem (více fáze prohození)](#Multi-Phase).  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>Prohození s náhledem (více fáze swap)

Prohození s náhledem nebo více fáze prohození zjednodušit ověření konfigurace slotu specifické prvky, jako jsou například připojovací řetězce.
Pro klíčové úlohy, kterou chcete ověřit které aplikace se chová podle očekávání při použití konfigurace produkční slot a je třeba provést takové ověření *před* aplikace je prohodily do produkčního prostředí. Prohození s náhledem je, co potřebujete.

> [!NOTE]
> Prohození s náhledem nepodporuje webové aplikace v systému Linux.

Při použití **prohození s náhledem** možnost (najdete v části [Prohodit sloty nasazení](#Swap)), služby App Service provede následující akce:

- Uchová cílový slot beze změny, není ovlivněná existující zatížení na této pozici (například výroba).
- Aplikuje konfigurační prvky z cílového slotu na zdrojový slot, včetně specifické pro slot připojovací řetězce a nastavení aplikace.
- Restartování pracovních procesů na zdrojový slot, pomocí těchto zmíněnými konfigurace – elementy.
- Po dokončení prohození: Přesune warmed vedlejší-up zdrojový slot v cílovém slotu. Přesunutí cílového slotu na zdrojový slot jako ruční prohození.
- Když zrušíte prohození: znovu použije konfigurace prvků na zdrojový slot na zdrojový slot.

Můžete zobrazit náhled, přesně jak aplikace se bude chovat s konfigurací cílový slot. Po dokončení ověření dokončíte odkládacího souboru v samostatném kroku. Tento krok má výhodu na zdrojový slot již jestli s požadovanou konfigurací a klienti nebudou mít žádné výpadky.  

Ukázky pro rutiny prostředí Azure PowerShell k dispozici pro více fáze prohození jsou součástí rutin Azure Powershellu pro část sloty nasazení.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurace automatického prohození
Automatické prohození zjednodušuje DevOps scénáře, ve které chcete nepřetržitě nasazení vaší aplikace pomocí nulové studený start a brání výpadkům pro koncové zákazníky aplikace. Když slot nasazení je nakonfigurována pro automatické prohození do produkčního prostředí, pokaždé, když nabízená aktualizace kódu na tomto slot, služby App Service automaticky Prohodit aplikace do produkčního prostředí poté, co se má již provozní teplotu ve slotu.

> [!IMPORTANT]
> Když povolíte automaticky Prohodit slot, ujistěte se, že konfigurace slotu přesně konfigurace určené pro cílový slot (obvykle produkční slot).
> 
> 

> [!NOTE]
> Nepodporuje automatické prohození webové aplikace v systému Linux.

Konfigurace automatického Prohodit pro slot je snadné. Postupujte následovně:

1. V **nasazovací sloty**, vyberte mimo produkční slot a zvolit **nastavení aplikace** v okně prostředků tento slot.  
   
    ![][Autoswap1]
2. Vyberte **na** pro **Prohodit automaticky**, vyberte požadovaný cíl slot v **automaticky Prohodit Slot**a klikněte na tlačítko **Uložit** na panelu příkazů. Ujistěte se, že konfigurace pro přihrádky přesně konfigurace určené pro cílový slot.
   
    **Oznámení** karta bude flash zelená **úspěch** po dokončení operace.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > K testování Prohodit automaticky pro vaši aplikaci, vyberte nejprve mimo produkční cílový slot v **automaticky Prohodit Slot** se seznámit s funkcí.  
   > 
   > 
3. Spusťte push kódu na tomto slot nasazení. Automatického prohození se stane po krátkou dobu a aktualizace se projeví na adrese URL vaší cílový slot.

<a name="Rollback"></a>

## <a name="to-rollback-a-production-app-after-swap"></a>Vrátit zpět produkční aplikaci po swap
Pokud žádné chyby se zjišťují v produkčním prostředí po prohození slotů, vrátit přihrádkách zpátky do stavu před swap odkládací stejné dva sloty okamžitě.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Vlastní warm-up před swap
Některé aplikace může vyžadovat vlastní warm-up akce. `applicationInitialization` Konfiguračního prvku v souboru web.config. můžete zadat vlastní inicializaci akce dříve, než je žádost o přijetí. Operace přepnutí vyčká pro tuto vlastní warm-up pro dokončení. Zde je fragment ukázkový soubor web.config.

    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostname="[app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>

## <a name="to-delete-a-deployment-slot"></a>Chcete-li odstranit slotu nasazení
V okně pro slot nasazení, otevřete okno slotu nasazení, klikněte na **přehled** (výchozí stránka) a klikněte na tlačítko **odstranit** na panelu příkazů.  

![Odstranit slotu nasazení][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="azure-powershell-cmdlets-for-deployment-slots"></a>Rutiny Azure PowerShell pro nasazovací sloty
Prostředí Azure PowerShell je modul, který poskytuje rutiny pro správu Azure pomocí prostředí Windows PowerShell, včetně podpory pro správu nasazovací sloty ve službě Azure App Service.

* Informace o instalaci a konfiguraci prostředí Azure PowerShell a týkající se ověřování Azure PowerShell s předplatným Azure najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell Microsoft](/powershell/azure/overview).  

- - -
### <a name="create-a-web-app"></a>Vytvoření webové aplikace
```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Vytvořte slot nasazení
```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Zahájení prohození s náhledem (více fáze prohození) a použít konfiguraci cílového slotu na zdrojový slot
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Zrušit čeká na prohození (prohození s zkontrolujte) a obnovit konfiguraci slotu zdroje
```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Prohodit sloty nasazení
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="delete-deployment-slot"></a>Odstranit nasazovací slot.
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="azure-command-line-interface-azure-cli-commands-for-deployment-slots"></a>Azure příkazy rozhraní příkazového řádku (Azure CLI) pro nasazovací sloty
Rozhraní příkazového řádku Azure poskytuje příkazy a platformy pro práci s Azure, včetně podpory pro správu služby App Service nasazovací sloty.

* Pokyny týkající se instalace a konfigurace rozhraní příkazového řádku Azure, včetně informací o tom, jak připojit k předplatnému Azure, rozhraní příkazového řádku Azure najdete v části [instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md).
* Chcete-li seznam příkazů, které jsou k dispozici pro službu Azure App Service v Azure CLI, volejte `azure site -h`.

> [!NOTE] 
> Pro [Azure CLI 2.0](https://github.com/Azure/azure-cli) příkazy pro nasazovací sloty, najdete v části [az webapp nasazovací slot](/cli/azure/webapp/deployment/slot).

- - -
### <a name="azure-site-list"></a>seznam webů Azure
Informace o aplikacích v aktuálním předplatném volání **seznam webů azure**, jako v následujícím příkladu.

`azure site list webappslotstest`

- - -
### <a name="azure-site-create"></a>Vytvoření Azure lokality
Chcete-li vytvořit slotu nasazení, volejte **azure lokality vytvořit** a zadejte název existující aplikaci a název slotu chcete vytvořit, jako v následujícím příkladu.

`azure site create webappslotstest --slot staging`

Pokud chcete povolit zdrojového kódu pro nový slot, použijte **– git** možnost, jako v následujícím příkladu.

`azure site create --git webappslotstest --slot staging`

- - -
### <a name="azure-site-swap"></a>swap Azure lokality
Chcete-li aktualizované nasazení slot na produkční aplikaci, použijte **azure lokality swap** příkazu provést operaci přepnutí, jako v následujícím příkladu. Na produkční aplikaci nebude mít žádný výpadek ani ho určitým studený start.

`azure site swap webappslotstest`

- - -
### <a name="azure-site-delete"></a>odstranění webu Azure
Pokud chcete odstranit slot nasazení, který již není potřeba, použijte **odstranění azure webu** příkazu, jako v následujícím příkladu.

`azure site delete webappslotstest --slot staging`

- - -
> [!NOTE]
> Sledujte webovou aplikaci v akci. [Vyzkoušet službu App Service](https://azure.microsoft.com/try/app-service/) okamžitě a vytvořit krátkodobou úvodní aplikaci – nepožaduje se žádná platební karta a bez jakýchkoli závazků.
> 
> 

## <a name="next-steps"></a>Další kroky
[Azure App Service webové aplikace – blok webového přístupu k testovacím nasazovací sloty](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)
[Úvod do služby App Service v systému Linux](../app-service/containers/app-service-linux-intro.md)
[bezplatná zkušební verze Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/)

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

