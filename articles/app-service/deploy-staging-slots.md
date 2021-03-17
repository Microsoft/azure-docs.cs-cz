---
title: Nastavení přípravného prostředí
description: Naučte se nasazovat aplikace do neprodukčního slotu a autoswap do produkčního prostředí. Zvyšte spolehlivost a Eliminujte výpadky aplikací z nasazení.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 1c4cff264b63506432daf350be3557bae7234584
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594235"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Nastavení přípravných prostředí ve službě Azure App Service
<a name="Overview"></a>

Když nasadíte webovou aplikaci, webovou aplikaci v systému Linux, back-end Mobile nebo aplikaci API na [Azure App Service](./overview.md), můžete místo výchozího produkčního slotu použít samostatný slot pro nasazení, když pracujete na úrovni plánu **Standard**, **Premium** nebo **Isolated** App Service. Sloty nasazení jsou živé aplikace s vlastními názvy hostitelů. Prvky obsahu aplikace a konfigurace je možné prohodit mezi dvěma sloty nasazení, včetně produkčního slotu. 

Nasazení aplikace do neprodukčního slotu má následující výhody:

* Změny aplikace můžete ověřit v pracovním slotu nasazení před jejich přepnutím do produkčního slotu.
* Když nasadíte aplikaci do slotu a zaměníte ji do produkčního prostředí, zajistíte, aby se všechny instance slotu před jejich přepnutím do produkčního prostředí zahodily. Tím se eliminuje prostoje při nasazení aplikace. Přesměrování provozu je bezproblémové a žádné požadavky nejsou vyřazeny z důvodu operací prohození. Tento celý pracovní postup můžete automatizovat konfigurací [automatického prohození](#Auto-Swap) , pokud není nutné ověření předem.
* Po prohození teď má slot s dřív připravenou aplikací předchozí produkční aplikaci. Pokud se změny vyměněné do produkčního slotu neočekávají, můžete stejnou záměnu provést hned a získat tak svůj "poslední známý dobrý web" zpátky.

Každá úroveň plánu App Service podporuje jiný počet slotů nasazení. Za použití slotů nasazení se neúčtují žádné další poplatky. Pokud chcete zjistit počet slotů, které podporuje vrstva vaší aplikace, přečtěte si téma [omezení App Service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

Pokud chcete aplikaci škálovat na jinou úroveň, ujistěte se, že cílová vrstva podporuje počet slotů, které už vaše aplikace používá. Pokud má vaše aplikace například více než pět slotů, nemůžete ji škálovat na úroveň **Standard** , protože úroveň **Standard** podporuje jenom pět slotů nasazení. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Přidat slot
Aby bylo možné povolit více slotů nasazení, musí být aplikace spuštěná v úrovni **Standard**, **Premium** nebo **izolovaná** .


1. v [Azure Portal](https://portal.azure.com/)vyhledejte a vyberte **App Services** a vyberte svou aplikaci. 
   
    ![Hledat App Services](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. V levém podokně vyberte **nasazovací sloty**  >  **Přidat slot**.
   
    ![Přidání nového slotu nasazení](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Pokud aplikace ještě není v úrovni **Standard**, **Premium** nebo **izolovaná** , zobrazí se zpráva, která indikuje podporované úrovně pro povolení vystavení při dvoufázovém publikování. V tomto okamžiku máte možnost vybrat **upgrade** a před pokračováním přejít na kartu **škálování** aplikace.
   > 

3. V dialogovém okně **Přidat slot** zadejte název slotu a vyberte, jestli se má naklonovat konfigurace aplikace z jiného slotu nasazení. Pokračujte výběrem **Přidat** .
   
    ![Zdroj konfigurace](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Můžete naklonovat konfiguraci z jakékoli existující patice. Nastavení, která je možné klonovat, zahrnují nastavení aplikace, připojovací řetězce, verze jazykových rozhraní, webové sokety, verzi HTTP a bitová verze platformy.

4. Po přidání slotu vyberte **Zavřít** , aby se dialogové okno zavřelo. Nový slot se teď zobrazuje na stránce **sloty nasazení** . Ve výchozím nastavení je **přenos%** nastaven na hodnotu 0 pro novou patici a veškerý provoz zákazníka je směrován do produkčního slotu.

5. Výběrem nového slotu nasazení otevřete stránku prostředků této patice.
   
    ![Název slotu nasazení](./media/web-sites-staged-publishing/StagingTitle.png)

    Pracovní slot má stránku správy stejně jako jakoukoli jinou aplikaci App Service. Můžete změnit konfiguraci slotu. Pokud chcete připomenout, že zobrazujete slot nasazení, název aplikace se zobrazí jako **\<app-name>/\<slot-name>** a typ aplikace je **App Service (slot)**. Slot se taky dá ve vaší skupině prostředků zobrazit jako samostatná aplikace se stejnými označeními.

6. Vyberte adresu URL aplikace na stránce prostředku slotu. Slot nasazení má svůj vlastní název hostitele a zároveň je to živá aplikace. Pokud chcete omezit veřejný přístup k slotu nasazení, přečtěte si téma [Azure App Service omezení IP adres](app-service-ip-restrictions.md).

Nový slot pro nasazení nemá žádný obsah, i když naklonujte nastavení z jiné patice. Můžete například [publikovat na tuto pozici v Gitu](./deploy-local-git.md). Do slotu se dá nasadit z jiné větve úložiště nebo z jiného úložiště.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Co se stane během prohození

### <a name="swap-operation-steps"></a>Kroky operace prohození

Když provedete prohozením dvou slotů (obvykle z přípravného slotu do produkčního slotu), App Service provede následující kroky, které zajistí, že cílový slot nefunguje bez výpadku:

1. Použijte následující nastavení z cílového slotu (například z produkčního slotu) na všechny instance zdrojové patice: 
    - Nastavení aplikace [konkrétního slotu](#which-settings-are-swapped) a připojovací řetězce, pokud jsou k dispozici.
    - Nastavení [průběžného nasazování](deploy-continuous-deployment.md) , pokud je povoleno.
    - [App Service nastavení ověřování](overview-authentication-authorization.md) , pokud je povoleno.
    
    Kterýkoli z těchto případů aktivuje všechny instance ve zdrojové patici, aby se restartovaly. Během [prohození s náhledem](#Multi-Phase)označuje konec první fáze. Operace swapu je pozastavená a můžete ověřit, jestli zdrojové sloty správně funguje s nastaveními cílové patice.

1. Počkejte na všechny instance ve zdrojové patici, aby se dokončilo restartování. Pokud se nepovede restartování jakékoli instance, operace přepnutí vrátí všechny změny zdrojové patice a zastaví operaci.

1. Pokud je povolena [místní mezipaměť](overview-local-cache.md) , spusťte inicializaci místní mezipaměti tím, že na každou instanci zdrojové patice nastavíte požadavek HTTP na kořen aplikace ("/"). Počkejte, dokud každá instance nevrátí žádnou odpověď HTTP. Inicializace místní mezipaměti způsobí další restartování každé instance.

1. Pokud je [Automatické prohození](#Auto-Swap) povoleno s [vlastním zahříváním](#Warm-up), spusťte spuštění [aplikace](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) spuštěním požadavku HTTP do kořenového adresáře aplikace ("/") v každé instanci zdrojové patice.

    Pokud `applicationInitialization` není zadaný, spusťte požadavek HTTP do kořenového adresáře aplikace zdrojové patice každé instance. 
    
    Pokud instance vrátí odpověď HTTP, je považována za zahřívání.

1. Pokud se všechny instance ve zdrojové patici zahřívá úspěšně, Proměňte tyto dva sloty tak, že přepnete pravidla směrování pro tyto dva sloty. Po provedení tohoto kroku bude mít cílová patice (například produkční slot) aplikaci, která byla dříve zahřívání ve zdrojové patici.

1. Teď, když má zdrojový slot předem prohozenou aplikaci v cílové patici, proveďte stejnou operaci pomocí všech nastavení a restartováním instancí.

V jakémkoli okamžiku swapu se veškerá práce s inicializací vyměněných aplikací stane ve zdrojové pozici. Cílový slot zůstane online, zatímco se zdrojové sloty připravují a zahřívá, bez ohledu na to, kde se prohození zdaří nebo selže. Pokud chcete vyměnit pracovní slot s produkčním slotem, ujistěte se, že produkční slot je vždycky cílový slot. Tato operace přepnutí nijak neovlivní vaši produkční aplikaci.

### <a name="which-settings-are-swapped"></a>Která nastavení jsou zahozena?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Pokud chcete nakonfigurovat nastavení aplikace nebo připojovací řetězec tak, aby přešel na konkrétní slot (neswaped), na stránce **Konfigurace** této přihrádky se dostanete. Přidejte nebo upravte nastavení a pak vyberte **nastavení slotu nasazení**. Zaškrtnutí tohoto políčka informuje App Service, že nastavení nelze měnit. 

![Nastavení slotu](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Prohození dvou slotů 
Na stránce **sloty nasazení** vaší aplikace a na stránce **Přehled** můžete vyměnit sloty nasazení. Technické podrobnosti o prohození slotu najdete v tématu [co se stane při prohození](#AboutConfiguration).

> [!IMPORTANT]
> Před zavedením aplikace z slotu nasazení do produkčního prostředí se ujistěte, že je produkční a že všechna nastavení ve zdrojové pozici jsou nakonfigurovaná přesně tak, jak je chcete mít v produkčním prostředí.
> 
> 

Postup při prohození slotů nasazení:

1. Přejít na stránku **sloty nasazení** vaší aplikace a vyberte **swap**.
   
    ![Tlačítko pro přepnutí](./media/web-sites-staged-publishing/SwapButtonBar.png)

    Dialogové okno **swap** zobrazuje nastavení ve vybraných zdrojových a cílových slotech, které budou změněny.

2. Vyberte požadované **zdrojové** a **cílové** sloty. Obvykle je cílem produkční slot. Také vyberte karty **změny zdrojového kódu** a **cíl změny** a ověřte, zda byly provedeny změny konfigurace. Až budete hotovi, můžete sloty hned vyměnit tak, že vyberete **swap**.

    ![Dokončení výměny slotů](./media/web-sites-staged-publishing/SwapImmediately.png)

    Pokud chcete zjistit, jak by se cílový slot spouštěl s novým nastavením před tím, než dojde ke skutečnému prohození, nevybírejte možnost **swap**, ale postupujte podle pokynů v části [prohození s náhledem](#Multi-Phase)

3. Až skončíte, zavřete dialogové okno tak, že vyberete **Zavřít**.

Pokud máte nějaké problémy, přečtěte si téma [řešení potíží se zahozením](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Prohodit ve verzi Preview (vícenásobné swapy)

Před přepnutím do produkčního prostředí jako cílového slotu ověřte, že aplikace běží s vyměněným nastavením. Zdrojové sloty se také zahřeje před dokončením swapu, což je žádoucí pro klíčové aplikace.

Když provedete prohození s náhledem, App Service provede stejnou [operaci přepnutí](#AboutConfiguration) , ale po prvním kroku se pozastaví. Před dokončením prohození pak můžete ověřit výsledek na přípravném slotu. 

Pokud zrušíte prohození, App Service znovu aplikuje prvky konfigurace na zdrojovou pozici.

Pro prohození s náhledem:

1. Postupujte podle kroků v části [prohození slotů nasazení](#Swap) , ale vyberte **provést prohození s náhledem**.

    ![Prohodit s náhledem](./media/web-sites-staged-publishing/SwapWithPreview.png)

    V dialogovém okně se dozvíte, jak se změní konfigurace ve zdrojovém slotu ve fázi 1 a jak se změní zdrojový a cílový slot ve fázi 2.

2. Až budete připraveni zahájit prohození, vyberte možnost **Spustit prohození**.

    Po dokončení fáze 1 se zobrazí upozornění v dialogovém okně. Zobrazte náhled swapu ve zdrojovém slotu tak, že na `https://<app_name>-<source-slot-name>.azurewebsites.net` . 

3. Až budete připraveni dokončit vyřazení, vyberte **Dokončit prohození** v **akci prohození** a vyberte **Dokončit prohození**.

    Chcete-li zrušit probíhající zahození, vyberte možnost **Zrušit zaměnit** místo.

4. Až skončíte, zavřete dialogové okno tak, že vyberete **Zavřít**.

Pokud máte nějaké problémy, přečtěte si téma [řešení potíží se zahozením](#troubleshoot-swaps).

Informace o automatizaci vícenásobného swapu najdete v tématu [automatizace pomocí PowerShellu](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Vrácení swapu zpět
Pokud dojde k nějakým chybám v cílové patici (například k produkčnímu slotu) po prohození slotu, obnovte tyto sloty do jejich předem odkládacích stavů tak, že tyto dva sloty hned odměňujete.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurace automatického prohození

> [!NOTE]
> Automatické prohození není podporováno ve webových aplikacích v systému Linux.

Automatické prohození zjednodušuje scénáře Azure DevOps, ve kterých chcete aplikaci průběžně nasadit s nulovým startem a s nulovými výpadky pro zákazníky aplikace. Když je automatický swap povolený z slotu do produkčního prostředí, pokaždé, když se do této patice nahraje změny kódu, App Service automaticky [zahodí aplikaci do produkčního prostředí](#swap-operation-steps) po zahřívání ve zdrojové pozici.

   > [!NOTE]
   > Před konfigurací automatického prohození pro produkční slot zvažte možnost testování automatického prohození na neprodukčním cílovém slotu.
   > 

Konfigurace automatického prohození:

1. Přejít na stránku prostředků vaší aplikace. Vyberte **Konfigurace slotů pro nasazení**  >  *\<desired source slot>*  >    >  **Obecné nastavení**.
   
2. Pro **Automatické prohození** vyberte **zapnuto**. Pak vyberte požadovanou cílovou patici pro **slot nasazení automatického prohození** a na panelu příkazů vyberte **Uložit** . 
   
    ![Výběry pro konfiguraci automatického prohození](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Spusťte do zdrojové patice nabízení kódu. Automatické prohození proběhne po krátké době a aktualizace se projeví na adrese URL cílové patice.

Pokud máte nějaké problémy, přečtěte si téma [řešení potíží se zahozením](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Zadat vlastní zahřívání

Některé aplikace mohou před zahozením vyžadovat vlastní akce. `applicationInitialization`Prvek konfigurace v web.config umožňuje určit vlastní inicializační akce. [Operace prohození](#AboutConfiguration) počká na dokončení tohoto vlastního zahřívání a teprve potom bude prohozena s cílovou paticí. Tady je ukázka fragmentu web.config.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostName="[app hostname]" />
    </applicationInitialization>
</system.webServer>
```

Další informace o přizpůsobení `applicationInitialization` prvku najdete v části Nejčastější [selhání přepnutí slotu nasazení a jejich](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)řešení.

Můžete také přizpůsobit chování zahřívání pomocí jednoho nebo obou následujících [nastavení aplikace](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Cesta k nástroji test pro zahřívání webu. Toto nastavení aplikace přidejte zadáním vlastní cesty, která začíná lomítkem jako hodnotou. Příklad: `/statuscheck`. Výchozí hodnota je `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Platné kódy odpovědí HTTP pro operaci zahřívání. Přidejte toto nastavení aplikace s čárkami odděleným seznamem kódů HTTP. Příklad je `200,202` . Pokud vrácený stavový kód není v seznamu, operace zahřívání a swap se zastaví. Ve výchozím nastavení jsou všechny kódy odpovědí platné.

> [!NOTE]
> `<applicationInitialization>`Prvek konfigurace je součástí spuštění každé aplikace, zatímco dvě nastavení aplikace pro zahřívání se vztahují jenom na zahození slotu.

Pokud máte nějaké problémy, přečtěte si téma [řešení potíží se zahozením](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitorování swapu

Pokud se [operace prohození](#AboutConfiguration) trvá příliš dlouho, můžete získat informace o operaci swapu v [protokolu aktivit](../azure-monitor/essentials/platform-logs-overview.md).

Na stránce prostředků vaší aplikace na portálu v levém podokně vyberte **Protokol aktivit**.

Operace swap se v dotazu protokolu zobrazí jako `Swap Web App Slots` . Můžete ho rozbalit a vybrat jednu z dílčích operací nebo chyb a zobrazit podrobnosti.

## <a name="route-traffic"></a>Směrování provozu

Ve výchozím nastavení se všechny požadavky klientů na produkční adresu URL () aplikace směrují `http://<app_name>.azurewebsites.net` do produkčního slotu. Část provozu můžete směrovat do jiné patice. Tato funkce je užitečná v případě, že potřebujete zpětnou vazbu od uživatele k nové aktualizaci, ale nejste připraveni ho uvolnit do produkčního prostředí.

### <a name="route-production-traffic-automatically"></a>Směrovat provozní provoz automaticky

Postup automatického směrování provozních přenosů:

1. Přejít na stránku prostředků vaší aplikace a vyberte **sloty nasazení**.

2. Ve sloupci **provoz%** slotu, na který chcete směrovat, zadejte procento (mezi 0 a 100), které bude představovat objem celkového provozu, který chcete směrovat. Vyberte **Uložit**.

    ![Nastavení procenta provozu](./media/web-sites-staged-publishing/RouteTraffic.png)

Po uložení nastavení se zadané procento klientů náhodně směruje do neprodukčního slotu. 

Po automatickém směrování klienta na konkrétní slot je tento slot "připnuté" do této patice po celou dobu trvání této klientské relace. V klientském prohlížeči uvidíte, ke kterému slotu je vaše relace připnuté, a Prohlédněte si `x-ms-routing-name` soubor cookie v hlavičkách protokolu HTTP. Požadavek, který je směrován do "přípravného" slotu, má soubor cookie `x-ms-routing-name=staging` . Požadavek, který je směrován do produkčního slotu, má soubor cookie `x-ms-routing-name=self` .

   > [!NOTE]
   > Vedle Azure Portal můžete pomocí [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) příkazu v Azure CLI nastavit procenta směrování z nástrojů CI/CD, jako jsou kanály DevOps nebo jiné systémy automatizace.
   > 

### <a name="route-production-traffic-manually"></a>Ruční směrování provozní provozu

Kromě automatického směrování provozu můžou App Service směrovat požadavky do konkrétního slotu. To je užitečné, když chcete, aby se vaši uživatelé mohli vyjádřit nebo odhlásit z vaší beta aplikace. Chcete-li směrovat provozní provoz ručně, použijte `x-ms-routing-name` parametr dotazu.

Pokud chcete uživatelům umožnit, aby si z aplikace nahlásili svůj souhlas, můžete tento odkaz umístit na svou webovou stránku:

```html
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Řetězec `x-ms-routing-name=self` určuje produkční slot. Po přístupu klientského prohlížeče k odkazu se přesměruje na produkční slot. Každý další požadavek obsahuje `x-ms-routing-name=self` soubor cookie, který zakládá relaci do produkčního slotu.

Pokud chcete uživatelům umožnit, aby se do aplikace beta přihlášeni, nastavte stejný parametr dotazu na název neprodukčního slotu. Tady je příklad:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Ve výchozím nastavení mají nové sloty pravidlo směrování `0%` , zobrazené v šedé. Když explicitně nastavíte tuto hodnotu na `0%` (zobrazený černý text), můžou uživatelé přistupovat k pracovnímu slotu ručně pomocí `x-ms-routing-name` parametru dotazu. Nebudou ale směrovány do slotu automaticky, protože procento směrování je nastaveno na 0. Toto je pokročilý scénář, ve kterém můžete "svůj pracovní slot" Skrýt z veřejného a zároveň umožnit interním týmům testování změn na slotu.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Odstranění slotu

Vyhledejte a vyberte svou aplikaci. Vyberte možnost **sloty nasazení**  >  *\<slot to delete>*  >  **– Přehled**. Typ aplikace se zobrazuje jako **App Service (slot)** , abyste se přihlásili, že prohlížíte slot pro nasazení. Na panelu příkazů vyberte **Odstranit** .  

![Odstranění slotu nasazení](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizace pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell je modul, který poskytuje rutiny pro správu Azure prostřednictvím prostředí Windows PowerShell, včetně podpory pro správu slotů nasazení v Azure App Service.

Informace o instalaci a konfiguraci Azure PowerShell a o ověřování Azure PowerShell pomocí předplatného Azure najdete v tématu [Jak nainstalovat a nakonfigurovat Microsoft Azure PowerShell](/powershell/azure/).  

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
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Zahájit prohození s náhledem (vícenásobná swap) a použít konfiguraci cílového slotu na zdrojový slot
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Zrušení nedokončeného zahození (prohození s revizí) a obnovení konfigurace zdrojového slotu
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Prohození slotů nasazení
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorování událostí prohození v protokolu aktivit
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Odstranění slotu
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatizace pomocí šablon Správce prostředků

[Šablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) jsou DEKLARATIVNÍ soubory JSON používané k automatizaci nasazení a konfigurace prostředků Azure. K prohození slotů pomocí Správce prostředků šablon nastavíte dvě vlastnosti na prostředky *Microsoft. Web/Sites/sloty* a *Microsoft. Web/Web* :

- `buildVersion`: Jedná se o řetězcovou vlastnost, která představuje aktuální verzi aplikace nasazené ve slotu. Například: "v1", "1.0.0.1" nebo "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: Jedná se o řetězcovou vlastnost, která určuje, co `buildVersion` má slot mít. Pokud se targetBuildVersion neshoduje s aktuálním `buildVersion` , aktivuje se operace přepnutí tím, že najde pozici zadané patice `buildVersion` .

### <a name="example-resource-manager-template"></a>Příklad šablony Správce prostředků

Následující šablona Správce prostředků aktualizuje `buildVersion` pracovní slot a nastaví v `targetBuildVersion` produkčním slotu. Tím se tyto dva sloty zahodí. Šablona předpokládá, že už máte vytvořenou WebApp s slotem s názvem "fázování".

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

Tato Správce prostředků šablona je idempotentní, což znamená, že je možné ji provést opakovaně a získat stejný stav slotů. Po prvním spuštění `targetBuildVersion` se bude shodovat s aktuálním `buildVersion` , takže se neaktivuje swap.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizace pomocí rozhraní příkazového řádku

Příkazy rozhraní příkazového [řádku Azure](https://github.com/Azure/azure-cli) pro sloty nasazení najdete v tématu [AZ WebApp Deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Řešení potíží se zahozením

Pokud dojde k nějaké chybě během [prohození slotu](#AboutConfiguration), přihlásí se *D:\home\LogFiles\eventlog.xml*. Je také zaznamenána v protokolu chyb konkrétní aplikace.

Zde jsou některé běžné chyby swapu:

- Požadavku HTTP na kořen aplikace vypršel časový limit. Operace prohození čeká na každou žádost HTTP na 90 sekund a opakuje se až 5 časů. Pokud dojde k vypršení časového limitu pro všechny opakované pokusy, operace přepnutí se zastaví.

- Inicializace místní mezipaměti může selhat, pokud obsah aplikace překračuje kvótu místního disku zadanou pro místní mezipaměť. Další informace najdete v tématu [Přehled místní mezipaměti](overview-local-cache.md).

- Při [vlastním zahřívání](#Warm-up)se požadavky HTTP provádějí interně (bez průchodu externí adresou URL). Můžou selhat s určitými pravidly pro přepsání adresy URL v *Web.config*. Například pravidla pro přesměrování názvů domén nebo vynucení HTTPS můžou zabránit zahřívání požadavků, aby dosáhly kódu aplikace. Pokud chcete tento problém obejít, upravte pravidla přepsání přidáním následujících dvou podmínek:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Bez vlastního zahřívání můžou pravidla přepisu adresy URL pořád blokovat požadavky HTTP. Pokud chcete tento problém obejít, upravte pravidla přepsání přidáním následující podmínky:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- Po prohození slotu může aplikace zaznamenat neočekávané restartování. Důvodem je to, že po prohození není konfigurace vazeb názvů hostitelů synchronizovaná, což sám o sobě nezpůsobí restart. Některé zdrojové události úložiště (například převzetí služeb při selhání svazku úložiště) ale můžou detekovat rozdíly a vynutit restartování všech pracovních procesů. K minimalizaci těchto typů restartování nastavte [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` nastavení aplikace](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) na *všech slotech*. Toto nastavení *aplikace ale nefunguje s* aplikacemi Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Další kroky
[Blokovat přístup k neprodukčním slotům](app-service-ip-restrictions.md)