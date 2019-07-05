---
title: Nastavení přípravných prostředí pro web apps ve službě Azure App Service | Dokumentace Microsoftu
description: Naučte se používat fázované publikování webových aplikací ve službě Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: fd488d475e24bc1aeebfa49b9d81b04ebae449ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445599"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Nastavení přípravných prostředí ve službě Azure App Service
<a name="Overview"></a>

Při nasazení webové aplikace, webové aplikace v Linuxu, mobilních back-end nebo aplikaci API [služby Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), můžete použít samostatné slotu místo produkčního slotu výchozí když spouštíte v **Standard**, **Premium**, nebo **izolované** úroveň plánu služby App Service. Sloty nasazení jsou živé aplikace s vlastní názvy hostitelů. Elementy obsahu a konfigurace aplikace je možné Prohodit mezi dvěma sloty nasazení, včetně produkčního slotu. 

Nasazení aplikace do slotu nevýrobní prostředí má následující výhody:

* Před přepnutím pomocí produkčního slotu můžete ověřit změny aplikace do přípravného slotu nasazení.
* Nasazení aplikace do slotu nejprve a přepnutím do produkčního prostředí zajišťuje, že všechny instance slotu zahřejí před přepnutím do produkčního prostředí. Tím se eliminuje prostoje při nasazení vaší aplikace. Přesměrování provozu je bezproblémové a žádné žádosti jsou vyřazena z důvodu operace prohození. Tento celého pracovního postupu můžete automatizovat pomocí konfigurace [automatické prohození](#Auto-Swap) když není potřeba prohození předběžné ověření.
* Po převodu se slot s dříve připravenou aplikace teď má předchozí produkční aplikace. Nejsou-li změny přepnutím do produkčního slotu podle očekávání, můžete provést stejný prohození okamžitě zobrazíte "poslední známé dobré webu" zpět.

Každá úroveň plánu služby App Service podporuje jiný počet nasazovacích slotů. Neexistuje žádné další poplatky za používání slotů nasazení. Chcete zjistit počet slotů vaší aplikace úroveň podporuje, najdete v článku [limity služby App Service](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). 

Umožní škálování vaší aplikace na jinou úroveň, ujistěte se, že na cílové úrovni podporuje počtu slotů, které už aplikace používá. Například pokud vaše aplikace obsahuje více než pět sloty, nelze ji převedete na **standardní** vrstvy, protože **standardní** úroveň podporuje se jenom pět sloty nasazení. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Přidat slot
Aplikace musí být spuštěn v **standardní**, **Premium**, nebo **izolované** úrovně měli povolit několik nasazovacích slotů.

1. V [webu Azure portal](https://portal.azure.com/), otevřete v této aplikaci [stránka s materiály pro](../azure-resource-manager/manage-resources-portal.md#manage-resources).

2. V levém podokně vyberte **sloty nasazení** > **přidat Slot**.
   
    ![Přidat nový slot nasazení](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Pokud ještě není v aplikaci **standardní**, **Premium**, nebo **izolované** úrovně, se zobrazí zpráva, která určuje podporované úrovně pro povolení fázované publikování. V tomto okamžiku máte možnost vybrat si **upgradovat** a přejděte na **škálování** kartu vaší aplikace, než budete pokračovat.
   > 

3. V **přidat slot** dialogové okno, zadejte název slotu a vyberte, jestli se má klonovat konfiguraci služby aplikace z jiného slot pro nasazení. Vyberte **přidat** pokračujte.
   
    ![Zdroj konfigurace](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Můžete naklonovat konfiguraci z jakékoli existujícího slotu. Nastavení, která se dají klonovat zahrnují nastavení aplikace, připojovacích řetězců, jazykové verze rozhraní framework, webové sokety, verze protokolu HTTP a bitové verze platformy.

4. Po přidání slotu vyberte **zavřete** zavřete dialogové okno. Nový slot se teď zobrazují v **sloty nasazení** stránky. Ve výchozím nastavení **provoz %** je nastavena na hodnotu 0 pro nový slot se všech zákazníků provoz směrovat na produkční slot.

5. Vyberte nový slot nasazení otevřete stránka s materiály pro tento slot.
   
    ![Název slotu nasazení](./media/web-sites-staged-publishing/StagingTitle.png)

    Přípravný slot má stránku správy stejně jako jakoukoli jinou aplikaci služby App Service. Můžete změnit konfiguraci slotu. Název slotu zobrazuje v horní části stránky s připomínkou, že si prohlížíte slotu nasazení.

6. Vyberte adresu URL aplikace na stránce prostředků pro slot. Slot nasazení má svůj vlastní název hostitele a také živé aplikace. Omezit veřejný přístup k slot pro nasazení, naleznete v tématu [omezení IP adres Azure App Service](app-service-ip-restrictions.md).

Nový slot nasazení nemá žádný obsah, i když naklonujete nastavení z různých slot. Například můžete [publikovat tento slot s Gitem](app-service-deploy-local-git.md). Můžete nasadit na pozici v jiném úložišti větev nebo jiném úložišti. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Co se stane během prohození

### <a name="swap-operation-steps"></a>Kroky operace prohození

Když můžete přepínat mezi dvěma sloty (obvykle z přípravný slot na produkční slot), služby App Service provede následující kroky k zajištění, že cílový slot nemá dojít k výpadku:

1. Použijte následující nastavení v cílovém slotu (například produkčního slotu) na všechny instance zdrojový slot: 
    - [Specifické pro slot](#which-settings-are-swapped) nastavení aplikace a připojovacích řetězců, pokud je k dispozici.
    - [Průběžné nasazování](deploy-continuous-deployment.md) nastavení, pokud je povoleno.
    - [Ověřování pomocí služby App Service](overview-authentication-authorization.md) nastavení, pokud je povoleno.
    
    Všech těchto případech aktivovat všechny instance ve zdrojový slot restartovat. Během [prohození s náhledem](#Multi-Phase), to označuje konec první fázi. Operace prohození je pozastavená, a můžete ověřit, že zdrojový slot správně funguje s nastavením cílovém slotu.

1. Počkejte, každá instance v zdrojový slot dokončit jeho restartování. Pokud se restartování nepovede jakoukoli instanci, operace prohození se vrátí všechny změny na zdrojový slot a zastaví prováděnou operaci.

1. Pokud [místní mezipaměť](overview-local-cache.md) je povoleno, spustí inicializace místní mezipaměti tak, že požadavek HTTP do kořenového adresáře aplikace ("/") pro každou instanci zdrojový slot. Počkejte, dokud vrátí každá instance odpovědi HTTP. Inicializace místní mezipaměti způsobí, že dalšímu restartování pro každou instanci.

1. Pokud [automatické prohození](#Auto-Swap) je povolená s [vlastní zahřívání](#Warm-up), aktivační událost [inicializace aplikace](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) tím, že požadavek HTTP do kořenového adresáře aplikace ("/") pro každou instanci zdroje slot.

    Pokud `applicationInitialization` není zadán, aktivuje požadavek HTTP do kořenového adresáře aplikace zdrojový slot pro každou instanci. 
    
    Pokud instance vrátí všechny odpovědi HTTP, má se za to být provozní teplotu.

1. Pokud všechny instance na zdrojový slot zahřejí úspěšně, přepnutím pravidla směrování pro dvěma sloty Prohodit dvěma sloty. Po provedení tohoto kroku cílovém slotu (například produkčního slotu) je aplikace, která je dříve provozní teplotu v zdrojový slot.

1. Teď, když zdrojový slot má aplikace před prohození dříve v cílovém slotu, proveďte stejnou operaci i tak, že všechna nastavení a restartuje se instance.

V libovolném bodě operaci prohození všechny práci inicializaci byl prohozen. aplikace se na zdrojový slot. Cílový slot zůstávají online, zatímco se zdrojový slot připravili a provozní teplotu, bez ohledu na to, kde prohození úspěšná nebo neúspěšná. Lze provést výměnu přípravný slot s produkčního slotu, zajistěte, aby produkční slot vždy cílovém slotu. Tímto způsobem operaci prohození nemá vliv na vaše produkční aplikace.

### <a name="which-settings-are-swapped"></a>Nastavení, které jsou přehozeny?
Při klonování konfiguraci z jiného slot nasazení klonovaného konfigurace je upravovat. Některé konfigurační prvky podle obsahu napříč odkládacího souboru (ne slot konkrétní), zatímco ostatní prvky konfigurace budete mít všechno pod stejný slot. po prohození (slot konkrétní). Následující seznamy shrnují nastavení, které se mění při prohození slotů.

**Nastavení, která jsou přehozeny**:

* Obecná nastavení, například verzi rozhraní framework, 32 nebo 64-bit, webové sokety
* Nastavení aplikace (lze nakonfigurovat pro zůstanou slotu)
* Připojovací řetězce (může být nakonfigurovaný zůstanou slotu)
* Mapování obslužných rutin
* Nastavení monitorování a diagnostiky
* Veřejné certifikáty
* Obsah WebJobs
* Hybridní připojení *
* Integrace služby Virtual network *
* Koncové body služby *
* Azure Content Delivery Network *

Funkce s hvězdičkou (*) se plánuje na nastavit vždy navrchu do přihrádky. 

**Nastavení, která nejsou Prohodit**:

* Publikování koncových bodů
* Vlastní názvy domén
* Privátní certifikáty a vazby SSL
* Nastavení škálování
* WebJobs plánovače
* Omezení IP adres
* Stálé připojení
* Nastavení protokolu (HTTPS, verze protokolu TLS, klientské certifikáty)
* Nastavení diagnostického protokolu
* Prostředků mezi zdroji (CORS) pro sdílení obsahu

<!-- VNET and hybrid connections not yet sticky to slot -->

Pokud chcete nakonfigurovat aplikaci nastavení nebo připojovací řetězec se toho držet konkrétní slot (ne Prohodit), přejděte na **konfigurace** stránky pro tento slot. Přidat nebo upravit nastavení a pak vyberte **nastavení slotu nasazení**. Výběrem tohoto zaškrtávacího políčka informuje službu App Service, toto nastavení není vyměnitelné. 

![Nastavení slotu](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Přepínat mezi dvěma sloty 
Ve vaší aplikaci můžete prohození slotů nasazení **sloty nasazení** stránky a **přehled** stránky. Technické podrobnosti o prohození slotu, naleznete v tématu [co se stane během převodu](#AboutConfiguration).

> [!IMPORTANT]
> Předtím, než aplikace ze slotu nasazení se přepnutí do produkčního prostředí, ujistěte se, že se cílový slot produkčního prostředí a že všechna nastavení v zdrojový slot je nakonfigurované přesně tak, jak chcete mít v produkčním prostředí.
> 
> 

Chcete-li prohození slotů nasazení:

1. Přejděte do své aplikace **sloty nasazení** stránku a vybrat **Prohodit**.
   
    ![Tlačítko Prohození](./media/web-sites-staged-publishing/SwapButtonBar.png)

    **Prohodit** dialogové okno zobrazuje nastavení v vybrané zdrojovém i cílovém slotu, které se změní.

2. Vyberte požadovaný **zdroj** a **cílové** sloty. Cílem je obvykle produkční slot. Kromě toho **změny zdrojového** a **změny cílového** karty a ověřte, že se očekává, změny konfigurace. Jakmile budete hotovi, sloty Prohodit okamžitě tak, že vyberete **prohození**.

    ![Dokončit prohození](./media/web-sites-staged-publishing/SwapImmediately.png)

    Pokud chcete zobrazit, jak cílovém slotu běžet s novým nastavením před prohození přesně se stane, nevybírejte **prohození**, postupujte podle pokynů, ale [prohození s náhledem](#Multi-Phase).

3. Jakmile budete hotovi, zavřete dialogové okno tak, že vyberete **zavřete**.

Pokud máte jakékoli problémy, přečtěte si [Poradce při potížích s záměna](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Prohození s náhledem (vícefázových swap)

> [!NOTE]
> Prohození s náhledem není podporované ve službě web apps v Linuxu.

Předtím, než je přepnutí do produkčního prostředí jako cílový slot, ověřte, že aplikace běží s nastavením byl prohozen. Zdrojový slot je také provozní teplotu, před dokončením odkládacího souboru, který je žádoucí, aby důležité podnikové aplikace.

Když je provést prohození s náhledem, App Service provede stejné [operace prohození](#AboutConfiguration) ale pozastaví po prvním krokem. Můžete si ověřit, výsledek pro přípravný slot před dokončením prohození. 

V případě zrušení prohození služby App Service na zdrojový slot znovu použije konfigurační prvky.

Chcete-li prohození s náhledem:

1. postupujte podle kroků v [prohození slotů nasazení](#Swap) ale vyberte **provést prohození s náhledem**.

    ![Prohození s náhledem](./media/web-sites-staged-publishing/SwapWithPreview.png)

    Dialogové okno se dozvíte, jak změny konfigurace v zdrojový slot ve fázi 1, a jak změnit zdrojový a cílový slot ve fázi 2.

2. Jakmile budete připraveni začít prohození, vyberte **začít prohození**.

    Po dokončení fáze 1, zobrazí se oznámení v dialogovém okně. Ve verzi Preview velikost odkládacího souboru v zdrojový slot tak, že přejdete do `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Až budete připravení dokončit prohození čekající na vyřízení, vyberte **dokončit prohození** v **akce prohození** a vyberte **dokončit prohození**.

    Chcete-li zrušit probíhající odkládacího souboru, vyberte **zrušit prohození** místo.

4. Jakmile budete hotovi, zavřete dialogové okno tak, že vyberete **zavřete**.

Pokud máte jakékoli problémy, přečtěte si [Poradce při potížích s záměna](#troubleshoot-swaps).

K automatizaci prohození více fázích, najdete v článku [automatizace pomocí prostředí PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Vrátit zpět o prohození
Pokud v cílovém slotu (například produkčního slotu) po prohození slotů dojde k nějaké chybě, obnovte slotů stavy jejich předběžné prohození pomocí stejné dvěma sloty výměny okamžitě.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurovat automatické prohození

> [!NOTE]
> Automatické prohození se nepodporuje ve službě web apps v Linuxu.

Automatické prohození zjednodušuje scénáře Azure DevOps, kde chcete nasadit aplikaci nepřetržitě s nulovou souvisejícím s úplným spuštěním a bez výpadků pro zákazníky, kteří aplikace. Pokud automatické prohození je povolená z slot do produkčního prostředí, pokaždé, když nasdílíš změny provedené změny kódu do tohoto slotu služby App Service automaticky [Zamění aplikace do produkčního prostředí](#swap-operation-steps) poté, co to je provozní teplotu v zdrojový slot.

   > [!NOTE]
   > Než začnete konfigurovat automatické prohození pro produkční slot, vezměte v úvahu testování automatické prohození o neprodukční cílovém slotu.
   > 

Postup konfigurace automatického prohození:

1. Přejděte na stránku prostředek vaší aplikace. Vyberte **sloty nasazení** >  *\<požadovaný zdrojový slot >*  > **konfigurace**  >  **Obecné nastavení**.
   
2. Pro **povolené automatické prohození**vyberte **na**. Pak vyberte požadovaného cílový slot pro **deployment slot pro automatické prohození**a vyberte **Uložit** na panelu příkazů. 
   
    ![Vybrané možnosti pro konfiguraci automatické prohození](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Spusťte kód push na zdrojový slot. Automatické prohození se stane po krátkou dobu a aktualizace se projeví na adrese URL cílovém slotu.

Pokud máte jakékoli problémy, přečtěte si [Poradce při potížích s záměna](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Zadejte vlastní zahřívání
Když používáte [automatické prohození](#Auto-Swap), některé aplikace mohou vyžadovat akce vlastní zahřívání před prohození. `applicationInitialization` Konfiguračního prvku v souboru web.config umožňuje zadat vlastní inicializaci akce. [Operace prohození](#AboutConfiguration) počká tento vlastní zahřívání dokončení prohození se slotem cíl. Tady je ukázka fragmentu souboru web.config.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Další informace o přizpůsobení `applicationInitialization` prvku, naleznete v tématu [nejběžnějších selhání prohození slotu nasazení a jak je opravit](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Můžete také přizpůsobit chování zahřívání s jedno nebo obě z následujících [nastavení aplikace](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Cesta k odeslání příkazu ping zahřívání vašeho webu. Přidáte nastavení aplikace tak, že zadáte vlastní cestu, která začíná s lomítkem, jako hodnotu. Příklad: `/statuscheck`. Výchozí hodnota je `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Platný kódy odpovědí protokolu HTTP pro operace zahřívání. Přidáte nastavení aplikace s čárkou oddělený seznam kódů HTTP. Příkladem je `200,202` . Pokud vrácený kód stavu není v seznamu, zastaví se zahřívání a přepnutí operace. Ve výchozím nastavení všechny kódy odpovědí jsou platné.

Pokud máte jakékoli problémy, přečtěte si [Poradce při potížích s záměna](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitorování prohození

Pokud [operace prohození](#AboutConfiguration) trvá dlouhou dobu pro dokončení, můžete získat informace o operaci prohození [protokolu aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Na stránce prostředků vaší aplikace na portálu, v levém podokně vyberte **protokolu aktivit**.

Operace prohození se objeví v protokolu dotazu jako `Swap Web App Slots`. Můžete ji rozbalte a vyberte jeden z dílčích operací nebo chyby, pokud chcete zobrazit podrobnosti.

## <a name="route-traffic"></a>Směrování provozu

Ve výchozím nastavení všechny požadavky na adresu URL produkční aplikace (`http://<app_name>.azurewebsites.net`) jsou směrovány na produkční slot. Část provozu může směrovat na jinou pozici. Tato funkce je užitečná, pokud je třeba zpětné vazby uživatelů pro nové aktualizace, ale nejste připravení vydání do produkčního prostředí.

### <a name="route-production-traffic-automatically"></a>Automaticky směrovat provoz produkčního prostředí

Směrování provozu produkční automaticky:

1. Přejděte na stránku prostředek vaší aplikace a vyberte **sloty nasazení**.

2. V **provoz %** sloupec slotu, které chcete směrovat, zadejte procento (mezi 0 a 100) k reprezentaci množství celkového provozu, kterým chcete směrovat. Vyberte **Uložit**.

    ![Nastavení procento provozu](./media/web-sites-staged-publishing/RouteTraffic.png)

Po uložení nastavení stanovené procento klientů se náhodně směruje do mimo produkční slot. 

Po klienta je automaticky směrují na konkrétní pozici jeho "připojené" do tohoto slotu trvání relace klienta. Na klientském prohlížeči, zobrazí se slot, které vaše relace je připnutá k zobrazením `x-ms-routing-name` souborů cookie do vašeho hlavičky protokolu HTTP. Požadavek, který se směruje do slotu "staging" má soubor cookie `x-ms-routing-name=staging`. Požadavek, který se směruje na produkční slot má soubor cookie `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Směrování provozu produkční ručně

Kromě směrování automatické provozu služby App Service směrovat požadavky na konkrétní pozici. To je užitečné, pokud mají vaši uživatelé moci vyjádřit výslovný souhlas nebo vyjádřit výslovný nesouhlas beta verze aplikace. Směrování provozu produkční ručně, můžete použít `x-ms-routing-name` parametr dotazu.

Umožňuje uživatelům vyjádřit výslovný nesouhlas beta verze aplikace, například můžete umístit tento odkaz na webovou stránku:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Řetězec `x-ms-routing-name=self` určuje produkční slot. Po prohlížeče klienta přistupuje k odkazu, je přesměrován na produkční slot. Má každý další požadavek `x-ms-routing-name=self` souboru cookie, který připíná relace na produkční slot.

Chcete-li umožnit uživatelům přihlášení do aplikace pro beta, nastavte na název o neprodukční slotu stejný parametr dotazu. Tady je příklad:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Ve výchozím nastavení, jsou uvedeny nové sloty pravidlo směrování z `0%`, zobrazené šedě. Když explicitně nastavíte tuto hodnotu na `0%` (viz černý text), uživatelé mají přístup ke přípravný slot ručně pomocí `x-ms-routing-name` parametr dotazu. Ale proto nebudou směrovány do přihrádky automaticky protože směrování procento je nastavena na hodnotu 0. Toto je pokročilý scénář, kde můžete "Skrýt" vaší přípravný slot před veřejností zároveň umožní interními týmy a otestujte změny ve slotu.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Odstranit slot

Přejděte na stránku prostředek vaší aplikace. Vyberte **sloty nasazení** >  *\<slotu odstranit >*  > **přehled**. Vyberte **odstranit** na panelu příkazů.  

![Odstranění slotu nasazení](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizace pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prostředí Azure PowerShell je modul, který obsahuje rutiny pro správu Azure pomocí Windows Powershellu, včetně podpory ke správě slotů nasazení v Azure App Service.

Informace o instalaci a konfiguraci prostředí Azure PowerShell a na ověřování prostředí Azure PowerShell ve vašem předplatném Azure, najdete v části [instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Vytvoření webové aplikace
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Vytvořit slot
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Zahájení prohození s náhledem (vícefázových swap) a použít konfigurace cílového slotu na zdrojový slot
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Zrušit čekající prohození (prohození s revizí) a obnovení konfigurace zdrojový slot
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Prohození slotů nasazení
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorování odkládacího souboru událostí v protokolu aktivit
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Odstranit slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizace pomocí rozhraní příkazového řádku

Pro [rozhraní příkazového řádku Azure](https://github.com/Azure/azure-cli) příkazy pro sloty nasazení, najdete v článku [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Řešení potíží s záměna

Pokud dojde k chybě během [prohození slotů](#AboutConfiguration), je přihlášen *D:\home\LogFiles\eventlog.xml*. Je také zaznamenána v protokolu chyb specifické pro aplikaci.

Tady jsou některé běžné chyby odkládacího souboru:

- Požadavek HTTP do kořenového adresáře aplikace je vypršel časový limit. Operace prohození čeká 90 sekund pro každý požadavek HTTP a opakované pokusy až 5krát. Pokud všechny opakované pokusy jsou vypršení časového limitu, operace prohození se zastaví.

- Inicializace místní mezipaměti může selhat, pokud obsah aplikace překročí kvótu místního disku pro místní mezipaměť. Další informace najdete v tématu [přehled o místní mezipaměti](overview-local-cache.md).

- Během [vlastní zahřívání](#Warm-up), jsou vytvářeny interně (bez nutnosti kontaktovat externí adresu URL) požadavky HTTP. Může selhat s určitá pravidla pro přepis adres URL v *Web.config*. Například pravidla pro přesměrování názvy domén nebo vynucování HTTPS můžete zabránit zahřívání požadavků dosáhnout kódu aplikace. Chcete-li tento problém obejít, upravte pravidla pro přepis adres přidáním těchto dvou případů:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Bez vlastní zahřívání pravidla pro přepis adres URL stále blokují požadavky HTTP. Chcete-li tento problém obejít, upravte pravidla pro přepis adres tak, že přidáte následující podmínky:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Některé [pravidel omezení IP](app-service-ip-restrictions.md) by mohly bránit operaci prohození v odesílání požadavků HTTP do vaší aplikace. Rozsahy IPv4 adres, které začínají `10.` a `100.` jsou interní v nasazení. Byste měli povolit jim připojení k vaší aplikaci.

## <a name="next-steps"></a>Další postup
[Blokovat přístup pro neprodukční sloty](app-service-ip-restrictions.md)
