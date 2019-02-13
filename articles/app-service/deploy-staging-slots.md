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
ms.date: 01/03/2019
ms.author: cephalin
ms.openlocfilehash: 7c12b34f6d735579326d4ccdd95e7831fbb777d6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181418"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Nastavení přípravných prostředí ve službě Azure App Service
<a name="Overview"></a>

> [!NOTE]
> Tato příručka ukazuje, jak ke správě slotů pomocí nové stránky správy ve verzi preview. Zákazníci používají na existující stránku pro správu můžete dál používat stránce stávající správu slotu jako předtím, než. 
>

Když nasadíte webovou aplikaci, webové aplikace v Linuxu, mobilních back-endu a aplikace API k [služby App Service](https://go.microsoft.com/fwlink/?LinkId=529714), můžete nasadit do samostatného nasazení slotu místo produkčního slotu výchozí při spuštění v **standardní**, **Premium**, nebo **izolované** úroveň plánu služby App Service. Sloty nasazení jsou ve skutečnosti živé aplikace s vlastními názvy hostitele. Elementy obsahu a konfigurace aplikace je možné Prohodit mezi dvěma sloty nasazení, včetně produkčního slotu. Nasazení aplikace do slotu nevýrobní prostředí má následující výhody:

* Před přepnutím pomocí produkčního slotu můžete ověřit změny aplikace do přípravného slotu nasazení.
* Nasazení aplikace do slotu nejprve a přepnutím do produkčního prostředí zajišťuje, že všechny instance slotu zahřejí před přepnutím do produkčního prostředí. Tím se eliminuje prostoje při nasazení vaší aplikace. Přesměrování provozu je bezproblémové a žádné žádosti jsou vyřazena z důvodu operace prohození. Tento celý pracovní postup je možné automatizovat pomocí konfigurace [automatického prohození](#Auto-Swap) když není potřeba prohození předběžné ověření.
* Po převodu se slot s dříve připravenou aplikace teď má předchozí produkční aplikace. Nejsou-li změny přepnutím do produkčního slotu podle očekávání, můžete provést stejný prohození okamžitě zobrazíte "poslední známé dobré webu" zpět.

Každá úroveň plánu služby App Service podporuje jiný počet nasazovacích slotů. Chcete zjistit počet slotů vaší aplikace úroveň podporuje, najdete v článku [omezení služby App](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Umožní škálování vaší aplikace do jiné úrovně, musí podporovat na cílové úrovni počtu slotů, které už aplikace používá. Například pokud vaše aplikace obsahuje více než pět sloty, nelze ji převedete na **standardní** vrstvy, protože **standardní** úroveň podporuje pouze pět sloty nasazení.

<a name="Add"></a>

## <a name="add-slot"></a>Přidat slot
Aplikace musí být spuštěn v **standardní**, **Premium**, nebo **izolované** úrovně měli povolit několik nasazovacích slotů.

1. V [webu Azure portal](https://portal.azure.com/), otevřete v této aplikaci [stránka s materiály pro](../azure-resource-manager/resource-group-portal.md#manage-resources).

2. V levém navigačním panelu zvolte **nasazovací sloty (Preview)** možnost a potom klikněte na **přidat Slot**.
   
    ![Přidat nový slot nasazení](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Pokud ještě není v aplikaci **standardní**, **Premium**, nebo **izolované** vrstvy, obdržíte zprávu s oznámením podporované úrovně pro povolení fázované publikování. V tomto okamžiku máte možnost vybrat si **upgradovat** a přejděte do **škálování** kartu vaší aplikace, než budete pokračovat.
   > 

3. V **přidat slot** dialogové okno, zadejte název slotu a vyberte, jestli se má klonovat konfigurace aplikace z jiného existujícího slotu nasazení. Klikněte na tlačítko **přidat** pokračujte.
   
    ![Zdroj konfigurace](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Můžete naklonovat konfiguraci z jakékoli existujícího slotu. Nastavení, která se dají klonovat zahrnují nastavení aplikace, připojovacích řetězců, jazykové verze rozhraní framework, webové sokety, verze protokolu HTTP a bitové verze platformy.

4. Po přidání přihrádky, klikněte na tlačítko **zavřete** zavřete dialogové okno. Nový slot se teď zobrazují v **nasazovací sloty (Preview)** stránky. Ve výchozím nastavení **provoz %** je nastavena na hodnotu 0 pro nový slot se všech zákazníků provoz směrovat na produkční slot.

5. Klikněte na tlačítko Nový slot nasazení otevřete stránka s materiály pro tento slot.
   
    ![Název slotu nasazení](./media/web-sites-staged-publishing/StagingTitle.png)

    Přípravný slot má stránku správy stejně jako jakoukoli jinou aplikaci služby App Service. Můžete změnit konfiguraci slotu. Název slotu zobrazuje v horní části stránky s připomínkou, že si prohlížíte slotu nasazení.

6. Klikněte na adresu URL aplikace v stránka s materiály slot. Slot nasazení má svůj vlastní název hostitele a také živé aplikace. Omezit veřejný přístup k slot pro nasazení, naleznete v tématu [omezení IP adres služby Azure App](app-service-ip-restrictions.md).

Nový slot nasazení nemá žádný obsah, i když naklonujete nastavení z různých slot. Například můžete [publikovat tento slot s gitem](app-service-deploy-local-git.md). Můžete nasadit na pozici v jiném úložišti větev nebo jiném úložišti. 

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Nastavení, které jsou přehozeny?
Při klonování konfiguraci z jiného slot nasazení klonovaného konfigurace je upravovat. Kromě toho některé konfigurační prvky podle obsahu napříč odkládacího souboru (ne slot konkrétní) zatímco ostatní prvky konfigurace budete mít všechno pod stejný slot. po prohození (slot konkrétní). Následující seznamy shrnují nastavení, které se mění při prohození slotů.

**Nastavení, která jsou přehozeny**:

* Obecné nastavení – třeba framework verze, 32 nebo 64-bit, Web sockets
* Nastavení aplikace (lze nakonfigurovat pro zůstanou slotu)
* Připojovací řetězce (může být nakonfigurovaný zůstanou slotu)
* Mapování obslužných rutin
* Nastavení monitorování a diagnostiky
* Veřejné certifikáty
* Obsah WebJobs
* Hybridní připojení

**Nastavení, která nejsou Prohodit**:

* Publikování koncových bodů
* Custom Domain Names
* Privátní certifikáty a vazby SSL
* Nastavení škálování
* WebJobs plánovače

<!-- VNET, IP restrictions, CORS, hybrid connections? -->

Pokud chcete nakonfigurovat aplikaci nastavení nebo připojovací řetězec se toho držet konkrétní slot (ne Prohodit), přejděte na **nastavení aplikace** stránce tohoto slotu a pak vyberte **nastavení slotu** pole pro konfigurační prvky, které by měla zůstat na slot. Označení prvek konfigurace slotu konkrétní informuje službu App Service, že není vyměnitelné.

![Nastavení slotu](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Přepínat mezi dvěma sloty 
Ve vaší aplikaci můžete prohození slotů nasazení **nasazovací sloty (Preview)** stránky. 

Můžete také přepínat mezi sloty z **přehled** a **sloty nasazení** stránek, ale aktuálně umožňuje starší prostředí. Tato příručka předvádí, jak používat nové uživatelské rozhraní aplikace **nasazovací sloty (Preview)** stránky.

> [!IMPORTANT]
> Před přepnutí aplikace z slot nasazení do produkčního prostředí, ujistěte se, že všechna nastavení jsou nakonfigurována přesně tak, jak chcete mít v cíli prohození.
> 
> 

Prohození slotů nasazení, postupujte podle těchto kroků:

1. Přejděte do své aplikace **nasazovací sloty (Preview)** stránky a klikněte na tlačítko **Prohodit**.
   
    ![Tlačítko Prohození](./media/web-sites-staged-publishing/SwapButtonBar.png)

    **Prohodit** dialogové okno zobrazuje nastavení v vybrané zdrojovém i cílovém slotu, které se změní.

2. Vyberte požadovaný **zdroj** a **cílové** sloty. Cílem je obvykle produkční slot. Také, klikněte na tlačítko **změny zdrojového** a **změny cílového** karty a ověřte, že se očekává, změny konfigurace. Až budete hotovi, sloty Prohodit okamžitě kliknutím **prohození**.

    ![Dokončit prohození](./media/web-sites-staged-publishing/SwapImmediately.png)

    Pokud chcete zjistit, jak by cílový slot spustit s novým nastavením předtím, než se ve skutečnosti stane prohození, klikněte na nemáte **prohození**, postupujte podle pokynů, ale [prohození s náhledem](#Multi-Phase).

3. Jakmile budete hotovi, zavřete dialogové okno kliknutím **zavřete**.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Prohození s náhledem (vícefázových swap)

> [!NOTE]
> Prohození s náhledem není podporované ve službě web apps v Linuxu.

Před přechodem do produkčního prostředí jako cílový slot, ověřte spuštění aplikace s nastaveními byl prohozen. předtím, než se stane prohození. Zdrojový slot je také provozní teplotu, před dokončením odkládacího souboru, který je také vhodné pro klíčové aplikace.

Při provádění prohození s náhledem, provede služby App Service při spuštění prohození následující:

- Udržuje cílovém slotu beze změny, neovlivní existující úlohy na této pozici (například produkčního).
- Použije konfigurační prvky z cílového slotu na zdrojový slot, včetně specifické pro slot připojovací řetězce a nastavení aplikace.
- Restartování pracovních procesů na zdrojový slot pomocí tyto konfigurační prvky. Můžete procházet zdrojový slot a zobrazte aplikaci spustit změny konfigurace.

V případě velikost odkládacího souboru v samostatný krok, přejdeme zahřátého zdrojový slot na cílovém slotu a cílovém slotu na zdrojový slot. V případě zrušení prohození služby App Service na zdrojový slot znovu použije konfigurační prvky slotu zdroje.

Prohození s náhledem, postupujte podle těchto kroků.

1. postupujte podle kroků v [prohození slotů nasazení](#Swap) ale vyberte **provést prohození s náhledem**.

    ![Prohození s náhledem](./media/web-sites-staged-publishing/SwapWithPreview.png)

    Dialogové okno se dozvíte, jak změny konfigurace v zdrojový slot ve fázi 1, a jak změnit zdrojový a cílový slot ve fázi 2.

2. Až budete připraveni začít prohození, klikněte na tlačítko **začít prohození**.

    Po dokončení fáze 1, zobrazí se oznámení v dialogovém okně. Ve verzi Preview velikost odkládacího souboru v zdrojový slot tak, že přejdete do `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Až budete připraveni na dokončení probíhající odkládacího souboru, vyberte **dokončit prohození** v **akce prohození** a klikněte na tlačítko **dokončit prohození**.

    Chcete-li zrušit probíhající odkládacího souboru, vyberte **zrušit prohození** místo a klikněte na tlačítko **zrušit prohození**.

4. Jakmile budete hotovi, zavřete dialogové okno kliknutím **zavřete**.

K automatizaci prohození více fázích, najdete v článku automatizace pomocí prostředí PowerShell.

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>Vrátit zpět prohození
Pokud v cílovém slotu (například produkčního slotu) po prohození slotů dojde k nějaké chybě, obnovte slotů stavy jejich předběžné prohození pomocí stejné dvěma sloty výměny okamžitě.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurovat automatické prohození

> [!NOTE]
> Automatické prohození se nepodporuje ve službě web apps v Linuxu.

Automatické prohození zjednodušuje scénáře DevOps, ve které chcete pro koncové zákazníky aplikace nasadíte aplikaci nepřetržitě s nulovou úplné spuštění a bez výpadků. Při změně pozice autoswaps do produkčního prostředí, pokaždé, když nasdílíš změny kódu do tohoto slotu, App Service automaticky zamění aplikace do produkčního prostředí po jeho je provozní teplotu v zdrojový slot.

   > [!NOTE]
   > Než se nakonfigurují produkční slot automatického prohození, vezměte v úvahu testování automatického prohození o neprodukční cílovém slotu nejprve.
   > 

Pokud chcete nakonfigurovat automatické prohození, postupujte takto:

1. Přejděte na stránku prostředek vaší aplikace. Vyberte **nasazovací sloty (Preview)** > *\<požadovaný zdrojový slot >* > **nastavení aplikace**.
   
2. V **automatického prohození**vyberte **na**, pak vyberte požadovaného cílovém slotu v **automatického prohození slotu**a klikněte na tlačítko **Uložit** na panelu příkazů. 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Spusťte kód push na zdrojový slot. Automatické prohození se stane po krátkou dobu a aktualizace se projeví na adrese URL cílovém slotu.

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>Vlastní zahřívání
Při použití [Auto-Swap](#Auto-Swap), některé aplikace mohou vyžadovat akce vlastní zahřívání před prohození. `applicationInitialization` Konfiguračního prvku v souboru web.config umožňuje zadat vlastní inicializaci akce má být provedena. Operace prohození počká tento vlastní zahřívání až po dokončení prohození se slotem cíl. Tady je ukázka fragmentu souboru web.config.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Můžete také přizpůsobit chování zahřívání s jednou nebo více z následujících [nastavení aplikace](https://github.com/MicrosoftDocs/azure-docs-pr/pull/web-sites-configure.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Cesta k odeslání příkazu ping trvala Příprava na váš web. Přidáte nastavení aplikace tak, že zadáte vlastní cestu, která začíná s lomítkem, jako hodnotu. Například, `/statuscheck`. Výchozí hodnota je `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Platný kódy odpovědí protokolu HTTP pro operace zahřívání. Přidáte nastavení aplikace s čárkou oddělený seznam kódů HTTP. Příklad: `200,202` . Pokud vrácený kód stavu není v seznamu, zastaví se zahřívání a přepnutí operace. Ve výchozím nastavení všechny kódy odpovědí jsou platné.

## <a name="monitor-swap"></a>Monitorování odkládacího souboru

Pokud operace prohození trvá dlouhou dobu pro dokončení, můžete získat informace o operaci prohození [protokolu aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Na stránce prostředků vaší aplikace na portálu, v levém navigačním panelu vyberte **protokolu aktivit**.

Operace prohození se objeví v protokolu dotazu jako `Swap Web App Slots`. Můžete ji rozbalte a vyberte jeden z dílčích operací nebo chyby, pokud chcete zobrazit podrobnosti.

## <a name="route-traffic"></a>Směrování provozu

Ve výchozím nastavení všechny požadavky na adresu URL produkční aplikace (`http://<app_name>.azurewebsites.net`) jsou směrovány na produkční slot. Část provozu může směrovat na jinou pozici. Tato funkce je užitečná, pokud je třeba zpětné vazby uživatelů pro nové aktualizace, ale nejste připravení vydání do produkčního prostředí.

### <a name="route-production-traffic-automatically"></a>Automaticky směrovat provoz produkčního prostředí

Automaticky směrovat provoz produkčního prostředí, postupujte podle těchto kroků:

1. Přejděte na stránku prostředek vaší aplikace a vyberte **nasazovací sloty (Preview)**.

2. V **provoz %** sloupec slotu, které chcete směrovat, zadejte procento (mezi 0 a 100) k reprezentaci množství celkového provozu, kterým chcete směrovat. Klikněte na **Uložit**.

    ![](./media/web-sites-staged-publishing/RouteTraffic.png)

Po uložení nastavení stanovené procento klientů se náhodně směruje do mimo produkční slot. 

Jakmile klient se automaticky směrují na konkrétní pozici jeho "připojené" do tohoto slotu trvání relace klienta. Na klientském prohlížeči, zobrazí se slot, které vaše relace je připnutá k zobrazením `x-ms-routing-name` souborů cookie do vašeho hlavičky protokolu HTTP. Požadavek, který se směruje do slotu "staging" má soubor cookie `x-ms-routing-name=staging`. Požadavek, který se směruje na produkční slot má soubor cookie `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Směrování provozu produkční ručně

Kromě směrování automatické provozu služby App Service směrovat požadavky na konkrétní pozici. To je užitečné, pokud chcete uživatelům zajistit možnost být schopni připojil do souhlas nebo výslovný nesouhlas beta verze aplikace. Směrování provozu produkční ručně, můžete použít `x-ms-routing-name` parametr dotazu.

Umožňuje uživatelům vyjádřit výslovný nesouhlas beta verze aplikace, například můžete umístit tento odkaz na webové stránce:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Řetězec `x-ms-routing-name=self` určuje produkční slot. Jakmile prohlížeče klienta přistupuje k odkazu, je přesměrován na produkční slot nejen, ale má každý další požadavek `x-ms-routing-name=self` souboru cookie, který připíná relace na produkční slot.

Chcete-li umožnit uživatelům přihlášení do aplikace pro beta, nastavit stejný parametr dotazu k názvu bez produkčního slotu, například:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

<a name="Delete"></a>

## <a name="delete-slot"></a>Odstranit slot

Přejděte na stránku prostředek vaší aplikace. Vyberte **nasazovací sloty (Preview)** > *\<slotu odstranit >* > **přehled**. Klikněte na tlačítko **odstranit** na panelu příkazů.  

![Odstranění slotu nasazení](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizace pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prostředí Azure PowerShell je modul, který obsahuje rutiny pro správu Azure pomocí Windows Powershellu, včetně podpory ke správě slotů nasazení v Azure App Service.

Informace o instalaci a konfiguraci prostředí Azure PowerShell a na ověřování prostředí Azure PowerShell ve vašem předplatném Azure, najdete v části [instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-web-app"></a>Vytvoření webové aplikace
```PowerShell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-slot"></a>Vytvořit slot
```PowerShell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Zahájení prohození s náhledem (vícefázových swap) a použít konfigurace cílového slotu na zdrojový slot
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Zrušit probíhající odkládacího souboru (prohození s revizí) a obnovení konfigurace zdrojový slot
```PowerShell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Prohození slotů nasazení
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorování odkládacího souboru události v protokolu aktivit
```PowerShell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-slot"></a>Odstranit slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>Automatizace pomocí rozhraní příkazového řádku

Pro [rozhraní příkazového řádku Azure](https://github.com/Azure/azure-cli) příkazy pro sloty nasazení, najdete v článku [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>Další postup
[Blokovat přístup pro neprodukční sloty](app-service-ip-restrictions.md)
