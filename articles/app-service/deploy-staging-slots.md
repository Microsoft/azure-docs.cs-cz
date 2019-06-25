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
ms.openlocfilehash: cbf287aef2c1792033a198070da605014a7b6281
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272851"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Nastavení přípravných prostředí ve službě Azure App Service
<a name="Overview"></a>

Když nasadíte webovou aplikaci, webové aplikace v Linuxu, mobilních back-endu a aplikace API k [služby App Service](https://go.microsoft.com/fwlink/?LinkId=529714), můžete nasadit do samostatného nasazení slotu místo produkčního slotu výchozí při spuštění v **standardní**, **Premium**, nebo **izolované** úroveň plánu služby App Service. Sloty nasazení jsou ve skutečnosti živé aplikace s vlastními názvy hostitele. Elementy obsahu a konfigurace aplikace je možné Prohodit mezi dvěma sloty nasazení, včetně produkčního slotu. Nasazení aplikace do slotu nevýrobní prostředí má následující výhody:

* Před přepnutím pomocí produkčního slotu můžete ověřit změny aplikace do přípravného slotu nasazení.
* Nasazení aplikace do slotu nejprve a přepnutím do produkčního prostředí zajišťuje, že všechny instance slotu zahřejí před přepnutím do produkčního prostředí. Tím se eliminuje prostoje při nasazení vaší aplikace. Přesměrování provozu je bezproblémové a žádné žádosti jsou vyřazena z důvodu operace prohození. Tento celý pracovní postup je možné automatizovat pomocí konfigurace [automatického prohození](#Auto-Swap) když není potřeba prohození předběžné ověření.
* Po převodu se slot s dříve připravenou aplikace teď má předchozí produkční aplikace. Nejsou-li změny přepnutím do produkčního slotu podle očekávání, můžete provést stejný prohození okamžitě zobrazíte "poslední známé dobré webu" zpět.

Každá úroveň plánu služby App Service podporuje jiný počet nasazovacích slotů a neexistuje žádné další poplatky za používání slotů nasazení. Chcete zjistit počet slotů vaší aplikace úroveň podporuje, najdete v článku [omezení služby App](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Umožní škálování vaší aplikace do jiné úrovně, musí podporovat na cílové úrovni počtu slotů, které už aplikace používá. Například pokud vaše aplikace obsahuje více než pět sloty, nelze ji převedete na **standardní** vrstvy, protože **standardní** úroveň podporuje pouze pět sloty nasazení. 

<a name="Add"></a>

## <a name="add-slot"></a>Přidat slot
Aplikace musí být spuštěn v **standardní**, **Premium**, nebo **izolované** úrovně měli povolit několik nasazovacích slotů.

1. V [webu Azure portal](https://portal.azure.com/), otevřete v této aplikaci [stránka s materiály pro](../azure-resource-manager/manage-resources-portal.md#manage-resources).

2. V levém navigačním panelu zvolte **sloty nasazení** možnost a potom klikněte na **přidat Slot**.
   
    ![Přidat nový slot nasazení](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Pokud ještě není v aplikaci **standardní**, **Premium**, nebo **izolované** vrstvy, obdržíte zprávu s oznámením podporované úrovně pro povolení fázované publikování. V tomto okamžiku máte možnost vybrat si **upgradovat** a přejděte do **škálování** kartu vaší aplikace, než budete pokračovat.
   > 

3. V **přidat slot** dialogové okno, zadejte název slotu a vyberte, jestli se má klonovat konfigurace aplikace z jiného existujícího slotu nasazení. Klikněte na tlačítko **přidat** pokračujte.
   
    ![Zdroj konfigurace](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Můžete naklonovat konfiguraci z jakékoli existujícího slotu. Nastavení, která se dají klonovat zahrnují nastavení aplikace, připojovacích řetězců, jazykové verze rozhraní framework, webové sokety, verze protokolu HTTP a bitové verze platformy.

4. Po přidání přihrádky, klikněte na tlačítko **zavřete** zavřete dialogové okno. Nový slot se teď zobrazují v **sloty nasazení** stránky. Ve výchozím nastavení **provoz %** je nastavena na hodnotu 0 pro nový slot se všech zákazníků provoz směrovat na produkční slot.

5. Klikněte na tlačítko Nový slot nasazení otevřete stránka s materiály pro tento slot.
   
    ![Název slotu nasazení](./media/web-sites-staged-publishing/StagingTitle.png)

    Přípravný slot má stránku správy stejně jako jakoukoli jinou aplikaci služby App Service. Můžete změnit konfiguraci slotu. Název slotu zobrazuje v horní části stránky s připomínkou, že si prohlížíte slotu nasazení.

6. Klikněte na adresu URL aplikace v stránka s materiály slot. Slot nasazení má svůj vlastní název hostitele a také živé aplikace. Omezit veřejný přístup k slot pro nasazení, naleznete v tématu [omezení IP adres služby Azure App](app-service-ip-restrictions.md).

Nový slot nasazení nemá žádný obsah, i když naklonujete nastavení z různých slot. Například můžete [publikovat tento slot s gitem](app-service-deploy-local-git.md). Můžete nasadit na pozici v jiném úložišti větev nebo jiném úložišti. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-swap"></a>Co se stane během prohození

[Kroky operace prohození](#swap-operation-steps)
[nastavení, které jsou přehozeny?](#which-settings-are-swapped)

### <a name="swap-operation-steps"></a>Kroky operace prohození

Když můžete přepínat mezi dvěma sloty (obvykle z přípravný slot na produkční slot), služby App Service provede následující kroky k zajištění, že cílový slot nemá dojít k výpadku:

1. Použijte následující nastavení v cílovém slotu (např. produkční slot) na všechny instance zdrojový slot: 
    - [Specifické pro slot](#which-settings-are-swapped) nastavení aplikace a připojovacích řetězců, pokud je k dispozici.
    - [Průběžné nasazování](deploy-continuous-deployment.md) nastavení, pokud je povoleno.
    - [Ověřování pomocí služby App Service](overview-authentication-authorization.md) nastavení, pokud je povoleno.
    Některé z výše uvedených případech aktivuje všechny instance ve zdrojový slot restartovat. Během [prohození s náhledem](#Multi-Phase), to označuje konec první fáze, ve kterém je pozastavená operaci prohození a můžete ověřit, že zdrojový slot správně funguje s nastavením cílovém slotu.

1. Počkejte, každá instance v zdrojový slot dokončit jeho restartování. Pokud se restartování nepovede jakoukoli instanci, operace prohození se vrátí všechny změny na zdrojový slot a zruší operaci.

1. Pokud [místní mezipaměť](overview-local-cache.md) je povoleno, spustí inicializace místní mezipaměti tak, že HTTP požadavku do kořenového adresáře aplikace ("/") pro každou instanci zdrojový slot a počkejte, dokud vrátí každá instance odpovědi HTTP. Inicializace místní mezipaměti způsobí, že dalšímu restartování pro každou instanci.

1. Pokud [automatické prohození](#Auto-Swap) je povolená s [vlastní zahřívání](#custom-warm-up), aktivační událost [inicializace aplikace](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) tím, že požadavek HTTP do kořenového adresáře aplikace ("/") pro každou instanci zdroje slot. Pokud instance vrátí všechny odpovědi HTTP, má se za to být provozní teplotu.

    Pokud ne `applicationInitialization` je zadán, aktivuje požadavek HTTP do kořenového adresáře aplikace zdrojový slot pro každou instanci. Pokud instance vrátí všechny odpovědi HTTP, má se za to být provozní teplotu.

1. Pokud všechny instance na zdrojový slot zahřejí úspěšně, přepnutím pravidla směrování pro dvěma sloty Prohodit dvěma sloty. Po provedení tohoto kroku cílovém slotu (např. produkční slot) je aplikace, která je dříve provozní teplotu v zdrojový slot.

1. Teď, když zdrojový slot má aplikace před prohození dříve v cílovém slotu, proveďte stejnou operaci i tak, že všechna nastavení a restartuje se instance.

V libovolném bodě operace prohození se provádí veškerou práci inicializaci byl prohozen. aplikace na zdrojový slot. Cílový slot zůstávají online, zatímco se zdrojový slot připravené a zahřívají nahoru, bez ohledu na to kde prohození úspěšná nebo neúspěšná. Lze provést výměnu přípravný slot s produkčního slotu, zajistěte, aby produkční slot vždy cílovém slotu. Tímto způsobem, vaše produkční aplikace nemá vliv operaci prohození.

### <a name="which-settings-are-swapped"></a>Nastavení, které jsou přehozeny?
Při klonování konfiguraci z jiného slot nasazení klonovaného konfigurace je upravovat. Kromě toho některé konfigurační prvky podle obsahu napříč odkládacího souboru (ne slot konkrétní) zatímco ostatní prvky konfigurace budete mít všechno pod stejný slot. po prohození (slot konkrétní). Následující seznamy shrnují nastavení, které se mění při prohození slotů.

**Nastavení, která jsou přehozeny**:

* Obecné nastavení – třeba framework verze, 32 nebo 64-bit, Web sockets
* Nastavení aplikace (lze nakonfigurovat pro zůstanou slotu)
* Připojovací řetězce (může být nakonfigurovaný zůstanou slotu)
* Mapování obslužných rutin
* Nastavení monitorování a diagnostiky
* Veřejné certifikáty
* Obsah WebJobs
* Hybridní připojení *
* Integrace virtuální sítě *
* Service Endpoints *
* Azure CDN *

Funkce označené *, které budou vždy navrchu do přihrádky jsou naplánované. 

**Nastavení, která nejsou Prohodit**:

* Publikování koncových bodů
* Custom Domain Names
* Privátní certifikáty a vazby SSL
* Nastavení škálování
* WebJobs plánovače
* Omezení IP adres
* Stálé připojení
* Nastavení protokolu (HTTP**S**, verze protokolu TLS, klientské certifikáty)
* Nastavení diagnostického protokolu
* CORS

<!-- VNET and hybrid connections not yet sticky to slot -->

Pokud chcete nakonfigurovat aplikaci nastavení nebo připojovací řetězec se toho držet konkrétní slot (ne Prohodit), přejděte na **konfigurace** stránky pro tento slot, přidat nebo upravit nastavení a potom vyberte **nastavení slotu nasazení**pole. Výběrem toto zaškrtávací políčko informuje službu App Service, toto nastavení není vyměnitelné. 

![Nastavení slotu](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Přepínat mezi dvěma sloty 
Ve vaší aplikaci můžete prohození slotů nasazení **sloty nasazení** stránky a **přehled** stránky. Technické podrobnosti o prohození slotu, naleznete v tématu [co se stane během prohození](#what-happens-during-swap)

> [!IMPORTANT]
> Předtím, než aplikace ze slotu nasazení se přepnutí do produkčního prostředí, ujistěte se, že se cílový slot produkčního prostředí a že všechna nastavení v zdrojový slot je nakonfigurované přesně tak, jak chcete mít v produkčním prostředí.
> 
> 

Prohození slotů nasazení, postupujte podle těchto kroků:

1. Přejděte do své aplikace **sloty nasazení** stránky a klikněte na tlačítko **Prohodit**.
   
    ![Tlačítko Prohození](./media/web-sites-staged-publishing/SwapButtonBar.png)

    **Prohodit** dialogové okno zobrazuje nastavení v vybrané zdrojovém i cílovém slotu, které se změní.

2. Vyberte požadovaný **zdroj** a **cílové** sloty. Cílem je obvykle produkční slot. Také, klikněte na tlačítko **změny zdrojového** a **změny cílového** karty a ověřte, že se očekává, změny konfigurace. Až budete hotovi, sloty Prohodit okamžitě kliknutím **prohození**.

    ![Dokončit prohození](./media/web-sites-staged-publishing/SwapImmediately.png)

    Pokud chcete zjistit, jak by cílový slot spustit s novým nastavením předtím, než se ve skutečnosti stane prohození, klikněte na nemáte **prohození**, postupujte podle pokynů, ale [prohození s náhledem](#Multi-Phase).

3. Jakmile budete hotovi, zavřete dialogové okno kliknutím **zavřete**.

Pokud narazíte na nějaké problémy, přečtěte si téma [Poradce při potížích s záměna](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Prohození s náhledem (vícefázových swap)

> [!NOTE]
> Prohození s náhledem není podporované ve službě web apps v Linuxu.

Před přechodem do produkčního prostředí jako cílový slot, ověřte spuštění aplikace s nastaveními byl prohozen. předtím, než se stane prohození. Zdrojový slot je také provozní teplotu, před dokončením odkládacího souboru, který je také vhodné pro klíčové aplikace.

Když je provést prohození s náhledem, App Service provede stejné [operace prohození](#what-happens-during-swap) ale pozastaví po prvním krokem. Můžete si ověřit, výsledek pro přípravný slot před dokončením prohození. 

V případě zrušení prohození služby App Service na zdrojový slot znovu použije konfigurační prvky slotu zdroje.

Prohození s náhledem, postupujte podle těchto kroků.

1. postupujte podle kroků v [prohození slotů nasazení](#Swap) ale vyberte **provést prohození s náhledem**.

    ![Prohození s náhledem](./media/web-sites-staged-publishing/SwapWithPreview.png)

    Dialogové okno se dozvíte, jak změny konfigurace v zdrojový slot ve fázi 1, a jak změnit zdrojový a cílový slot ve fázi 2.

2. Až budete připraveni začít prohození, klikněte na tlačítko **začít prohození**.

    Po dokončení fáze 1, zobrazí se oznámení v dialogovém okně. Ve verzi Preview velikost odkládacího souboru v zdrojový slot tak, že přejdete do `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Až budete připraveni na dokončení probíhající odkládacího souboru, vyberte **dokončit prohození** v **akce prohození** a klikněte na tlačítko **dokončit prohození**.

    Chcete-li zrušit probíhající odkládacího souboru, vyberte **zrušit prohození** místo a klikněte na tlačítko **zrušit prohození**.

4. Jakmile budete hotovi, zavřete dialogové okno kliknutím **zavřete**.

Pokud narazíte na nějaké problémy, přečtěte si téma [Poradce při potížích s záměna](#troubleshoot-swaps).

K automatizaci prohození více fázích, najdete v článku automatizace pomocí prostředí PowerShell.

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>Vrátit zpět prohození
Pokud v cílovém slotu (například produkčního slotu) po prohození slotů dojde k nějaké chybě, obnovte slotů stavy jejich předběžné prohození pomocí stejné dvěma sloty výměny okamžitě.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurovat automatické prohození

> [!NOTE]
> Automatické prohození se nepodporuje ve službě web apps v Linuxu.

Automatické prohození zjednodušuje scénáře DevOps, ve které chcete pro koncové zákazníky aplikace nasadíte aplikaci nepřetržitě s nulovou úplné spuštění a bez výpadků. Pokud automatické prohození je povolená z slot do produkčního prostředí, pokaždé, když nasdílíš změny provedené změny kódu do tohoto slotu služby App Service automaticky [Zamění aplikace do produkčního prostředí](#swap-operation-steps) poté, co to je provozní teplotu v zdrojový slot.

   > [!NOTE]
   > Než nakonfigurujete automatické prohození pro produkční slot, zvažte nejprve testování automatické prohození o neprodukční cílovém slotu.
   > 

Pokud chcete nakonfigurovat automatické prohození, postupujte takto:

1. Přejděte na stránku prostředek vaší aplikace. Vyberte **sloty nasazení** >  *\<požadovaný zdrojový slot >*  > **konfigurace**  >  **Obecné nastavení**.
   
2. V **povolené automatické prohození**vyberte **na**, pak vyberte požadovaného cílovém slotu v **deployment slot pro automatické prohození**a klikněte na tlačítko **Uložit** v panel příkazů. 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Spusťte kód push na zdrojový slot. Automatické prohození se stane po krátkou dobu a aktualizace se projeví na adrese URL cílovém slotu.

Pokud narazíte na nějaké problémy, přečtěte si téma [Poradce při potížích s záměna](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>Vlastní zahřívání
Při použití [Auto-Swap](#Auto-Swap), některé aplikace mohou vyžadovat akce vlastní zahřívání před prohození. `applicationInitialization` Konfiguračního prvku v souboru web.config umožňuje zadat vlastní inicializaci akce má být provedena. [Operace prohození](#what-happens-during-swap) počká tento vlastní zahřívání až po dokončení prohození se slotem cíl. Tady je ukázka fragmentu souboru web.config.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Další informace o přizpůsobení `applicationInitialization` prvku, naleznete v tématu [nejběžnějších selhání prohození slotu nasazení a jak je opravit](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Můžete také přizpůsobit chování zahřívání s jednou nebo více z následujících [nastavení aplikace](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Cesta k odeslání příkazu ping trvala Příprava na váš web. Přidáte nastavení aplikace tak, že zadáte vlastní cestu, která začíná s lomítkem, jako hodnotu. Například, `/statuscheck`. Výchozí hodnota je `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Platný kódy odpovědí protokolu HTTP pro operace zahřívání. Přidáte nastavení aplikace s čárkou oddělený seznam kódů HTTP. Příklad: `200,202` . Pokud vrácený kód stavu není v seznamu, zastaví se zahřívání a přepnutí operace. Ve výchozím nastavení všechny kódy odpovědí jsou platné.

Pokud narazíte na nějaké problémy, přečtěte si téma [Poradce při potížích s záměna](#troubleshoot-swaps).

## <a name="monitor-swap"></a>Monitorování odkládacího souboru

Pokud [operace prohození](#what-happens-during-swap) trvá dlouhou dobu pro dokončení, můžete získat informace o operaci prohození [protokolu aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Na stránce prostředků vaší aplikace na portálu, v levém navigačním panelu vyberte **protokolu aktivit**.

Operace prohození se objeví v protokolu dotazu jako `Swap Web App Slots`. Můžete ji rozbalte a vyberte jeden z dílčích operací nebo chyby, pokud chcete zobrazit podrobnosti.

## <a name="route-traffic"></a>Směrování provozu

Ve výchozím nastavení všechny požadavky na adresu URL produkční aplikace (`http://<app_name>.azurewebsites.net`) jsou směrovány na produkční slot. Část provozu může směrovat na jinou pozici. Tato funkce je užitečná, pokud je třeba zpětné vazby uživatelů pro nové aktualizace, ale nejste připravení vydání do produkčního prostředí.

### <a name="route-production-traffic-automatically"></a>Automaticky směrovat provoz produkčního prostředí

Automaticky směrovat provoz produkčního prostředí, postupujte podle těchto kroků:

1. Přejděte na stránku prostředek vaší aplikace a vyberte **sloty nasazení**.

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

Ve výchozím nastavení, jsou uvedeny nové sloty pravidlo směrování z `0%`, zobrazené šedě. Explicitním nastavením této hodnoty na `0%` (viz černý text), uživatelé mají přístup ke přípravný slot ručně pomocí `x-ms-routing-name` parametr dotazu, ale nebudou směrovány do přihrádky automaticky protože směrování procento je nastaven na hodnotu 0. Toto je pokročilý scénář, kde můžete "Skrýt" vaší přípravný slot před veřejností zároveň umožní interními týmy a otestujte změny ve slotu.

<a name="Delete"></a>

## <a name="delete-slot"></a>Odstranit slot

Přejděte na stránku prostředek vaší aplikace. Vyberte **sloty nasazení** >  *\<slotu odstranit >*  > **přehled**. Klikněte na tlačítko **odstranit** na panelu příkazů.  

![Odstranění slotu nasazení](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizace pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prostředí Azure PowerShell je modul, který obsahuje rutiny pro správu Azure pomocí Windows Powershellu, včetně podpory ke správě slotů nasazení v Azure App Service.

Informace o instalaci a konfiguraci prostředí Azure PowerShell a na ověřování prostředí Azure PowerShell ve vašem předplatném Azure, najdete v části [instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-web-app"></a>Vytvoření webové aplikace
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-slot"></a>Vytvořit slot
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Zahájení prohození s náhledem (vícefázových swap) a použít konfigurace cílového slotu na zdrojový slot
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Zrušit probíhající odkládacího souboru (prohození s revizí) a obnovení konfigurace zdrojový slot
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Prohození slotů nasazení
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorování odkládacího souboru události v protokolu aktivit
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-slot"></a>Odstranit slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>Automatizace pomocí rozhraní příkazového řádku

Pro [rozhraní příkazového řádku Azure](https://github.com/Azure/azure-cli) příkazy pro sloty nasazení, najdete v článku [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Řešení potíží s záměna

Pokud dojde k chybě během [prohození slotů](#what-happens-during-swap), je přihlášen *D:\home\LogFiles\eventlog.xml*, a také v protokolu chyb specifické pro aplikaci.

Tady jsou některé běžné chyby odkládacího souboru:

- Požadavek HTTP do kořenového adresáře aplikace je vypršel časový limit. Operace prohození čeká 90 sekund pro každý požadavek HTTP a opakované pokusy až 5krát. Pokud všechny opakované pokusy jsou vypršení časového limitu, operace prohození byl přerušen.

- Inicializace místní mezipaměti může selhat, pokud obsah aplikace překročí kvótu místního disku pro místní mezipaměť. Další informace najdete v tématu [přehled o místní mezipaměti](overview-local-cache.md).

- Během [vlastní zahřívání](#custom-warm-up)interně (bez nutnosti kontaktovat externí adresu URL) jsou vytvářeny požadavky HTTP a může selhat s určité adresy URL pravidla pro přepis adres v *Web.config*. Například pravidla pro přesměrování názvy domén nebo vynucování HTTPS můžete zabránit zahřívání požadavků vůbec dosažení kódu aplikace. Chcete-li tento problém obejít, upravte pravidla pro přepis adres přidáním těchto dvou případů:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Bez vlastní zahřívání požadavků HTTP můžete stále uchovávat podle pravidla pro přepis adres URL. Chcete-li tento problém obejít, upravte pravidla pro přepis adres tak, že přidáte následující podmínky:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Některé [pravidel omezení IP](app-service-ip-restrictions.md) může zabránit operaci prohození v odesílání požadavků HTTP do vaší aplikace. Rozsahy IPv4 adres, které začínají `10.` a `100.` jsou interní v nasazení a má povolený pro připojení k vaší aplikace.

## <a name="next-steps"></a>Další postup
[Blokovat přístup pro neprodukční sloty](app-service-ip-restrictions.md)
