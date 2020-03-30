---
title: Nastavení pracovních prostředí
description: Zjistěte, jak nasadit aplikace do neprodukčního slotu a automaticky propojit do produkčního prostředí. Zvyšte spolehlivost a eliminujte prostoje aplikací z nasazení.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 03/04/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 21e025088e59c7f65f848b332ecb393b05918261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300839"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Nastavení přípravných prostředí ve službě Azure App Service
<a name="Overview"></a>

Když nasadíte webovou aplikaci, webovou aplikaci v Linuxu, mobilní back-end nebo aplikaci API do [služby Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), můžete použít samostatný slot pro nasazení namísto výchozího produkčního slotu, když běžíte na úrovni plánu **Standard**, **Premium**nebo **Isolated** App Service. Sloty pro nasazení jsou živé aplikace s vlastními názvy hostitelů. Obsah aplikace a prvky konfigurace lze přepínat mezi dvěma sloty nasazení, včetně produkčního slotu. 

Nasazení aplikace do neprodukčního slotu má následující výhody:

* Změny aplikací můžete ověřit v pracovním slotu nasazení před jejich prouspenze s produkčním slotem.
* Nasazení aplikace do slotu jako první a jeho přepnutí do produkčního prostředí zajišťuje, že všechny instance slotu se zahřejí před přepnutím do produkčního prostředí. To eliminuje prostoje při nasazení aplikace. Přesměrování provozu je bezproblémové a žádné požadavky jsou vynechány z důvodu swapových operací. Celý tento pracovní postup můžete automatizovat konfigurací [automatického prohození,](#Auto-Swap) když není potřeba ověření před prohození.
* Po prohození má slot s dříve připravenou aplikací nyní předchozí produkční aplikaci. Pokud změny převedené do produkčního slotu nejsou podle očekávání, můžete provést stejný swap okamžitě získat "poslední známé dobré místo" zpět.

Každá úroveň plánu služby App Service podporuje jiný počet slotů nasazení. Za používání slotů pro nasazení se neúčtuje žádný další poplatek. Pokud chcete zjistit počet slotů, které vaše aplikace podporuje, přečtěte si část [Omezení služby App Service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

Pokud chcete aplikaci škálovat na jinou úroveň, ujistěte se, že cílová úroveň podporuje počet slotů, které vaše aplikace už používá. Například pokud vaše aplikace má více než pět slotů, nemůžete škálovat ji na úroveň **Standard,** protože úroveň **Standard** podporuje pouze pět slotů nasazení. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Přidat slot
Aplikace musí být spuštěna na úrovni **Standard**, **Premium**nebo **Isolated,** abyste mohli povolit více slotů nasazení.


1. na [webu Azure Portal](https://portal.azure.com/)vyhledejte a vyberte **služby App Services** a vyberte aplikaci. 
   
    ![Hledání služeb aplikace](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. V levém podokně vyberte **Možnost Přidat patice** > **Add Slot**pro nasazení .
   
    ![Přidání nového slotu nasazení](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Pokud aplikace ještě není na úrovni **Standard**, **Premium**nebo **Isolated,** zobrazí se zpráva, která označuje podporované úrovně pro povolení fázovaného publikování. V tomto okamžiku máte možnost vybrat **upgrade** a přejděte na kartu **Škálování** aplikace, než budete pokračovat.
   > 

3. V **dialogovém okně Přidat patici** pojmenujte patici a vyberte, jestli chcete naklonovat konfiguraci aplikace z jiného slotu pro nasazení. Chcete-li pokračovat, vyberte **přidat.**
   
    ![Zdroj konfigurace](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Konfiguraci můžete naklonovat z libovolné existující patice. Mezi nastavení, která lze klonovat, patří nastavení aplikací, připojovací řetězce, verze jazykového frameworku, webové sokety, verze HTTP a bitness platformy.

4. Po přidání patice vyberte **Zavřít** a zavřete dialogové okno. Nový slot se nyní zobrazí na stránce **Nasazení slotů.** Ve výchozím nastavení je **hodnota provozu 0** pro nový slot, přičemž veškerý provoz zákazníka je směrován do výrobního slotu.

5. Vyberte nový slot nasazení pro otevření stránky prostředků této pozice.
   
    ![Název slotu pro nasazení](./media/web-sites-staged-publishing/StagingTitle.png)

    Pracovní slot má stránku pro správu stejně jako všechny ostatní aplikace App Service. Můžete změnit konfiguraci slotu. Název patice se zobrazí v horní části stránky, aby vám připomněl, že prohlížíte slot pro nasazení.

6. Vyberte adresu URL aplikace na stránce prostředků slotu. Slot pro nasazení má svůj vlastní název hostitele a je také živá aplikace. Pokud chcete omezit veřejný přístup k slotu pro nasazení, přečtěte si informace o [omezení IP služby Azure App Service](app-service-ip-restrictions.md).

Nový slot pro nasazení nemá žádný obsah, i když klonujete nastavení z jiného slotu. Můžete například [publikovat do tohoto slotu pomocí Gitu](app-service-deploy-local-git.md). Můžete nasadit do patice z jiné větve úložiště nebo jiného úložiště. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Co se stane během výměny

### <a name="swap-operation-steps"></a>Kroky operace prohození

Když zaměníte dva sloty (obvykle z pracovního slotu do produkčního slotu), služba App Service provádí následující kroky, aby zajistila, že cílový slot nezažije prostoje:

1. Použijte následující nastavení z cílového slotu (například produkčního slotu) na všechny instance zdrojového slotu: 
    - [Nastavení](#which-settings-are-swapped) aplikace specifické pro slot a připojovací řetězce, pokud je to možné.
    - [Průběžné nastavení nasazení,](deploy-continuous-deployment.md) pokud je povoleno.
    - [Nastavení ověřování služby App Service,](overview-authentication-authorization.md) pokud je povoleno.
    
    Některý z těchto případů aktivuje všechny instance ve zdrojovém slotu restartovat. Během [výměny s náhledem](#Multi-Phase)to znamená konec první fáze. Operace odhození je pozastavena a můžete ověřit, zda zdrojový slot pracuje správně s nastavením cílového slotu.

1. Počkejte, až každá instance ve zdrojovém slotu dokončí jeho restartování. Pokud se některá instance nepodaří restartovat, odkládací operace vrátí všechny změny do zdrojové patice a zastaví operaci.

1. Pokud je povolena [místní mezipaměť,](overview-local-cache.md) spusťte inicializaci místní mezipaměti vytvořením požadavku HTTP do kořenového adresáře aplikace ("/") v každé instanci zdrojové patice. Počkejte, dokud každá instance vrátí jakoukoli odpověď HTTP. Inicializace místní mezipaměti způsobí další restartování v každé instanci.

1. Pokud je [automatické prohození](#Auto-Swap) povoleno s [vlastním zahříváním](#Warm-up), [aktivujte iniciaci aplikace](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) vytvořením požadavku HTTP do kořenového adresáře aplikace ("/") v každé instanci zdrojové patice.

    Pokud `applicationInitialization` není zadán, aktivuj požadavek HTTP do kořenového adresáře aplikace zdrojové patice v každé instanci. 
    
    Pokud instance vrátí jakoukoli odpověď HTTP, považuje se za zahřátou.

1. Pokud jsou všechny instance ve zdrojové patici úspěšně zahřáté, vyměňte dva sloty přepnutím pravidel směrování pro dva sloty. Po tomto kroku cílový slot (například produkční slot) má aplikaci, která se dříve zahřála ve zdrojovém slotu.

1. Teď, když zdrojový slot má předswapovou aplikaci dříve v cílovém slotu, proveďte stejnou operaci použitím všech nastavení a restartováním instancí.

V libovolném okamžiku operace prohození se všechny práce inicializace prohozených aplikací děje ve zdrojovém slotu. Cílový slot zůstává online, zatímco zdrojový slot je připravován a zahříván, bez ohledu na to, kde se výměna uspěje nebo selže. Chcete-li vyměnit pracovní slot s produkčním slotem, ujistěte se, že produkční slot je vždy cílový slot. Tímto způsobem operace prohození nemá vliv na produkční aplikace.

### <a name="which-settings-are-swapped"></a>Která nastavení jsou prohozena?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Chcete-li nakonfigurovat nastavení aplikace nebo připojovací řetězec tak, aby se držely určitého slotu (ne zaměněné), přejděte na stránku **Konfigurace** pro daný slot. Přidejte nebo upravte nastavení a vyberte **nastavení slotu pro nasazení**. Zaškrtnutítohoto políčka informuje službu App Service, že nastavení nelze vyměnit. 

![Nastavení slotu](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Výměna dvou slotů 
Sloty nasazení můžete vyměnit na stránce **slotů nasazení** aplikace a na stránce **Přehled.** Technické podrobnosti o výměně slotů naleznete v tématu [Co se stane během prohození](#AboutConfiguration).

> [!IMPORTANT]
> Před výměnou aplikace z slotu nasazení do produkčního prostředí se ujistěte, že produkční verze je váš cílový slot a že všechna nastavení ve zdrojovém slotu jsou nakonfigurována přesně tak, jak chcete mít v produkčním prostředí.
> 
> 

Výměna slotů pro nasazení:

1. Přejděte na stránku **slotů nasazení** aplikace a vyberte **Swap**.
   
    ![Tlačítko Zaměnit](./media/web-sites-staged-publishing/SwapButtonBar.png)

    Dialogové okno **Zaměnit** se zobrazuje nastavení ve vybraných zdrojových a cílových slotech, které budou změněny.

2. Vyberte požadovaný **zdrojový** a **cílový** slot. Obvykle je cílem produkční slot. Vyberte také karty **Změny zdroje** a **Cílové změny** a ověřte, zda jsou očekávané změny konfigurace. Po dokončení můžete sloty okamžitě vyměnit výběrem možnosti **Swap**.

    ![Dokončení výměny slotů](./media/web-sites-staged-publishing/SwapImmediately.png)

    Chcete-li zjistit, jak by váš cílový slot běžel s novým nastavením před skutečně dojde k prohození, nevybírejte **swap**, ale postupujte podle pokynů v [prohození s náhledem](#Multi-Phase).

3. Po dokončení zavřete dialogové okno tak, že vyberete **Zavřít**.

Pokud máte nějaké problémy, [přečtěte si článek Poradce při potížích s odměňujením](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Výměna s náhledem (vícefázová výměna)

Před přepnutím do produkčního prostředí jako cílový slot ověřte, že aplikace běží s prohozené nastavení. Zdrojový slot je také zahřát před dokončením výměny, což je žádoucí pro kritické aplikace.

Když provedete prohození s náhledem, App Service provede stejnou [operaci odkládacího efektu,](#AboutConfiguration) ale po prvním kroku se pozastaví. Výsledek pak můžete ověřit v pracovním slotu před dokončením odkládacího bloku. 

Pokud zrušíte prohození, App Service znovu použije konfigurační prvky na zdrojový slot.

Výměna za náhled:

1. Postupujte podle kroků v [části Prohození slotů nasazení,](#Swap) ale vyberte **Provést prohození s náhledem**.

    ![Zaměnit s náhledem](./media/web-sites-staged-publishing/SwapWithPreview.png)

    Dialogové okno ukazuje, jak se mění konfigurace ve zdrojovém slotu ve fázi 1 a jak se změní zdrojová a cílová pozice ve fázi 2.

2. Až budete připraveni zahájit prohození, vyberte **Spustit prohození**.

    Po dokončení fáze 1 budete upozorněni v dialogovém okně. Náhled prohození ve zdrojovém slotu najdete na `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Až budete připraveni dokončit čekající odkládací, vyberte **Dokončit výměnu** v **akci Swap** a vyberte **Dokončit prohození**.

    Chcete-li zrušit čekající prohození, vyberte místo toho **zrušit prohození.**

4. Po dokončení zavřete dialogové okno tak, že vyberete **Zavřít**.

Pokud máte nějaké problémy, [přečtěte si článek Poradce při potížích s odměňujením](#troubleshoot-swaps).

Chcete-li automatizovat vícefázové prohození, přečtěte si informace [o automatizaci pomocí prostředí PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Vrácení výměny zpět
Pokud dojde k chybám v cílovém slotu (například produkční mašle) po výměně slotu, obnovte sloty do stavu před výměnou okamžitým prohozením stejných dvou slotů.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurace automatického prohození

> [!NOTE]
> Automatický swap není podporován ve webových aplikacích v Linuxu.

Automatické prohození zjednodušuje scénáře Azure DevOps, kde chcete aplikaci nasadit nepřetržitě s nulovými studenými starty a nulovými prostoji pro zákazníky aplikace. Když je automatické prohození povoleno z slotu do produkčního prostředí, pokaždé, když odešlete změny kódu do tohoto slotu, služba App Service automaticky [zamění aplikaci do produkčního prostředí](#swap-operation-steps) poté, co se zahřeje ve zdrojovém slotu.

   > [!NOTE]
   > Před konfigurací automatického prohození pro produkční slot zvažte testování automatického prohození na neprodukčním cílovém slotu.
   > 

Postup konfigurace automatického prohození:

1. Přejděte na stránku prostředků aplikace. Vyberte **možnost Nasazení slotů** > *\<požadovaný zdrojový slot>*  > **nastavení obecné** **konfigurace** > .
   
2. **Chcete-li automatické prohození povoleno**, vyberte možnost **Zapnuto**. Pak vyberte požadovanou cílovou pozici pro **slot pro automatické prohození a**vyberte **Uložit** na panelu příkazů. 
   
    ![Výběry pro konfiguraci automatického prohození](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Spusťte nabízený kód do zdrojové patice. Automatické prohození se stane po krátké době a aktualizace se projeví na adrese URL cílového slotu.

Pokud máte nějaké problémy, [přečtěte si článek Poradce při potížích s odměňujením](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Určení vlastního zahřívání

Některé aplikace mohou před výměnou vyžadovat vlastní zahřívací akce. Konfigurační `applicationInitialization` prvek v souboru web.config umožňuje zadat vlastní akce inicializace. [Operace odkládání](#AboutConfiguration) čeká na dokončení této vlastní zahřívací operace před prohození s cílovým slotem. Zde je ukázka web.config fragment.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Další informace o přizpůsobení `applicationInitialization` prvku naleznete v [tématu Nejběžnější selhání prohození slotů nasazení a jak je opravit](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Můžete také přizpůsobit zahřívací chování pomocí jednoho nebo obou následujících [nastavení aplikace](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Cesta k ping uzahřát vaše stránky. Toto nastavení aplikace přidejte zadáním vlastní cesty, která začíná lomítkem jako hodnotou. Příklad: `/statuscheck`. Výchozí hodnota je `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Platné kódy http odezvy pro zahřívací operaci. Přidejte toto nastavení aplikace se seznamem HTTP kódů oddělených čárkami. Příkladem je `200,202` . Pokud vrácený stavový kód není v seznamu, jsou zastaveny operace zahřívání a odkládacího stavu. Ve výchozím nastavení jsou všechny kódy odpovědí platné.

> [!NOTE]
> Konfigurační `<applicationInitialization>` prvek je součástí každého spuštění aplikace, zatímco dvě nastavení zahřívacího chování se vztahují pouze na zaměňovací sloty.

Pokud máte nějaké problémy, [přečtěte si článek Poradce při potížích s odměňujením](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Sledování výměny

Pokud [operace odhození](#AboutConfiguration) trvá dlouhou dobu, můžete získat informace o operaci odkládacího stavu v [protokolu aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Na stránce prostředků aplikace na portálu v levém podokně vyberte **Protokol aktivit**.

Odkládací operace se `Swap Web App Slots`zobrazí v protokolu dotazu jako . Můžete ji rozbalit a vybrat jednu z dílčích operací nebo chyb, abyste viděli podrobnosti.

## <a name="route-traffic"></a>Provoz na trase

Ve výchozím nastavení jsou všechny požadavky klientů na`http://<app_name>.azurewebsites.net`produkční adresu URL aplikace ( ) směrovány do produkčního slotu. Část provozu můžete směrovat do jiného slotu. Tato funkce je užitečná, pokud potřebujete zpětnou vazbu od uživatelů pro novou aktualizaci, ale nejste připraveni ji vydat do produkčního prostředí.

### <a name="route-production-traffic-automatically"></a>Automaticky směrovat výrobní provoz

Automatické směrování výrobního provozu:

1. Přejděte na stránku prostředků aplikace a vyberte **Sloty pro nasazení**.

2. Ve sloupci **Provoz %** v patici, do které chcete směrovat, zadejte procento (mezi 0 a 100), které bude představovat celkový provoz, který chcete směrovat. Vyberte **Uložit**.

    ![Nastavení procenta provozu](./media/web-sites-staged-publishing/RouteTraffic.png)

Po uložení nastavení je zadané procento klientů náhodně směrováno do neprodukčního slotu. 

Poté, co je klient automaticky směrován do určitého slotu, je "připnutý" k tomuto slotu po dobu životnosti této relace klienta. V prohlížeči klienta můžete zjistit, ke kterému slotu `x-ms-routing-name` je vaše relace připnutá, když se podíváte na soubor cookie v záhlaví http. Požadavek, který je směrován do "pracovní" `x-ms-routing-name=staging`slot má cookie . Požadavek, který je směrován do produkčního `x-ms-routing-name=self`slotu, má soubor cookie .

   > [!NOTE]
   > Vedle portálu Azure můžete taky [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) použít příkaz v rozhraní příkazu Azure cli nastavit procenta směrování z nástrojů CI/CD, jako jsou kanály DevOps nebo jiné automatizační systémy.
   > 

### <a name="route-production-traffic-manually"></a>Ruční směrování výrobního provozu

Kromě automatického směrování provozu může služba App Service směrovat požadavky na konkrétní slot. To je užitečné, pokud chcete, aby se uživatelé mohli přihlásit do vaší beta aplikace nebo se od ní odhlásit. Chcete-li ručně směrovat výrobní `x-ms-routing-name` provoz, použijte parametr dotazu.

Chcete-li uživatelům například povolit odhlášení z vaší beta aplikace, můžete tento odkaz umístit na webovou stránku:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Řetězec `x-ms-routing-name=self` určuje produkční slot. Poté, co prohlížeč klienta přistupuje k odkazu, je přesměrován do produkčního slotu. Každý následující požadavek má `x-ms-routing-name=self` soubor cookie, který připne relaci do produkčního slotu.

Chcete-li uživatelům povolit přihlášení k vaší beta aplikaci, nastavte stejný parametr dotazu na název neprodukčního slotu. Tady je příklad:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Ve výchozím nastavení jsou nové sloty `0%`uvedeny směrovací pravidlo , zobrazené šedě. Pokud explicitně nastavíte tuto hodnotu na `0%` (zobrazenou v černém textu), mohou uživatelé přistupovat k pracovní patici ručně pomocí parametru dotazu. `x-ms-routing-name` Ale nebudou směrovány do patice automaticky, protože procento směrování je nastaveno na 0. Toto je pokročilý scénář, kde můžete "skrýt" pracovní slot před veřejností a zároveň umožnit interním týmům testovat změny v patici.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Odstranění patice

Vyhledejte a vyberte aplikaci. Vyberte **slot Slot pro** > *\<nasazení, chcete-li odstranit>*  >  **Přehled**. Na panelu příkazů vyberte **Odstranit.**  

![Odstranění slotu pro nasazení](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizace pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell je modul, který poskytuje rutiny pro správu Azure přes Windows PowerShell, včetně podpory pro správu slotů nasazení ve službě Azure App Service.

Informace o instalaci a konfiguraci Azure PowerShellu a ověřování Azure PowerShellu pomocí předplatného Azure najdete v tématu [Jak nainstalovat a nakonfigurovat Microsoft Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Vytvoření webové aplikace
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Vytvoření slotu
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Zahájit výměnu s náhledem (vícefázový swap) a použít konfiguraci cílového slotu na zdrojový slot
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Zrušení čekajícího swapu (swap s revizí) a obnovení konfigurace zdrojového slotu
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Prohození slotů nasazení
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Sledování odkládací události v protokolu aktivit
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Odstranění patice
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatizace pomocí šablon Správce prostředků

[Šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) jsou deklarativní soubory JSON, které se používají k automatizaci nasazení a konfigurace prostředků Azure. Chcete-li vyměnit sloty pomocí šablon Správce prostředků, nastavíte dvě vlastnosti v prostředcích *Microsoft.Web/sites/slots* a *Microsoft.Web/sites:*

- `buildVersion`: Toto je vlastnost řetězce, která představuje aktuální verzi aplikace nasazené v patice. Například: "v1", "1.0.0.1" nebo "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: Toto je vlastnost string, která určuje, co `buildVersion` by měl mít patice. Pokud targetBuildVersion se nerovná `buildVersion`aktuální , pak to spustí operaci odkládací hledáním slot, který má zadaný `buildVersion`.

### <a name="example-resource-manager-template"></a>Ukázková šablona Správce prostředků

Následující šablona Správce prostředků `buildVersion` aktualizuje pracovní slot `targetBuildVersion` a nastaví na produkční množinu. Tím se vymění dva sloty. Šablona předpokládá, že již máte webapp vytvořený s slotem s názvem "staging".

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Tato šablona Správce prostředků je idempotentní, což znamená, že může být provedena opakovaně a vytvořit stejný stav slotů. Po prvním spuštění `targetBuildVersion` bude odpovídat aktuální `buildVersion`, takže swap nebude spuštěna.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizace pomocí cli

Příkazy [Příkazy k příkazu Azure](https://github.com/Azure/azure-cli) pro sloty nasazení najdete [v tématu slot pro nasazení az webapp](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Poradce při potížích s odměněními

Pokud během [odkládače slotu](#AboutConfiguration)dojde k nějaké chybě , je zaznamenána v *souboru D:\home\LogFiles\eventlog.xml*. Je také zaznamenána v protokolu chyb specifické pro aplikaci.

Zde jsou některé běžné chyby odkládacího

- Požadavek HTTP pro kořenové aplikace je časová. Operace odhození čeká 90 sekund pro každý požadavek HTTP a opakuje až 5 krát. Pokud jsou časový matné pokusy časovým režimem, je operace odkládacího režimu zastavena.

- Inicializace místní mezipaměti může selhat, pokud obsah aplikace překročí místní diskovou kvótu určenou pro místní mezipaměť. Další informace naleznete v tématu [Přehled místní mezipaměti](overview-local-cache.md).

- Během [vlastní warm-up](#Warm-up), http požadavky jsou prováděny interně (bez prochází externí URL). Mohou selhat s určitými pravidly přepisu adresy URL v *souboru Web.config*. Pravidla pro přesměrování názvů domén nebo vynucení protokolu HTTPS mohou například zabránit tomu, aby se žádosti o rozcvičku dostaly ke kódu aplikace. Chcete-li tento problém vyřešit, upravte pravidla přepisování přidáním následujících dvou podmínek:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Bez vlastního zahřívání mohou pravidla přepisu adresy URL stále blokovat požadavky HTTP. Chcete-li tento problém vyřešit, upravte pravidla přepisování přidáním následující podmínky:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Některá [pravidla omezení IP](app-service-ip-restrictions.md) může zabránit odkládací operace odesílání požadavků HTTP do vaší aplikace. Rozsahy adres IPv4, `10.` `100.` které začínají a jsou interní pro vaše nasazení. Měli byste jim povolit připojení k vaší aplikaci.

- Po prohození slotu může dojít k neočekávaným restartováním aplikace. Důvodem je, že po prohození konfigurace vazby název hostitele přejde synchronizace, což samo o sobě nezpůsobí restartování. Některé základní události úložiště (například převzetí služeb při selhání svazku úložiště) však může zjistit tyto nesrovnalosti a vynutit restartování všech pracovních procesů. Chcete-li minimalizovat tyto typy restartování, nastavte [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` nastavení aplikace](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) na *všech slotech*. Toto nastavení aplikace však *nefunguje* s aplikacemi Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Další kroky
[Blokovat přístup k neprodukčním slotům](app-service-ip-restrictions.md)
