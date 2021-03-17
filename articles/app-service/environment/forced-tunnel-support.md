---
title: Konfigurace vynuceného tunelování
description: Naučte se, jak App Service Environment fungovat při vynuceném tunelovém přenosu ve virtuální síti.
author: ccompy
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 95a4d00a27a0da363561f469b4c5e9e2ad16463c
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510494"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Konfigurace vynuceného tunelového propojení ve službě App Service Environment

App Service Environment (ASE) je nasazení služby Azure App Service ve virtuální síti Azure zákazníka. Mnoho zákazníků konfiguruje své virtuální sítě Azure jako rozšíření svých místních sítí pomocí sítí VPN nebo připojení Azure ExpressRoute. Vynucené tunelování znamená, že provoz nesměrujete do internetu, ale do své sítě VPN nebo svého virtuálního zařízení. Virtuální zařízení se často používají ke kontrole a auditování odchozího síťového provozu. 

Služba ASE má řadu externích závislostí popsaných v dokumentu [Architektura sítě služby App Service Environment][network]. Za normálních okolností musí veškerý odchozí provoz závislostí služby ASE procházet přes virtuální IP adresu zřízenou se službou ASE. Pokud změníte směrování provozu do nebo ze služby ASE, aniž byste se řídili níže uvedenými informacemi, vaše služba ASE přestane fungovat.

Ve virtuální síti Azure se směrování provádí na základě nejdelší shody předpony (LPM). Pokud existuje víc tras se stejnou shodou LPM, trasa se vybere na základě původu v tomto pořadí:

* Trasa definovaná uživatelem (UDR)
* Trasa protokolu BGP (pokud se používá služba ExpressRoute)
* Systémová trasa

Další informace o směrování ve virtuální síti najdete v tématu [Trasy definované uživatelem a předávání IP][routes]. 

Pokud chcete směrovat odchozí provoz služby ASE někam jinam než přímo do internetu, máte následující možnosti:

* Povolení přímého přístupu služby ASE k internetu
* Konfigurace ignorování tras protokolu BGP v podsíti služby ASE
* Konfigurace podsítě služby ASE pro používání koncových bodů služby pro SQL Azure a Azure Storage
* Přidání vlastních IP adres do brány firewall SQL Azure pro službu ASE

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Povolení přímého přístupu k internetu ve službě App Service Environment

Pokud chcete službě ASE povolit přímý přístup k internetu i v případě, že je ve vaší virtuální síti Azure nakonfigurované připojení ExpressRoute, můžete postupovat následovně:

* Nakonfigurujte připojení ExpressRoute, aby inzerovalo adresu 0.0.0.0/0. Ve výchozím nastavení směruje veškerý odchozí provoz do místní sítě.
* Vytvořte trasu UDR s předponou adresy 0.0.0.0/0 a dalším segmentem směrování typu internet a použijte ji pro podsíť služby ASE.

Pokud provedete tyto dvě změny, provoz směřující do internetu a pocházející z podsítě služby App Service Environment se nebude nuceně směrovat do připojení ExpressRoute.

Pokud už síť směruje provoz do místní sítě, musíte před pokusem o nasazení služby ASE vytvořit podsíť, která bude hostitelem služby ASE, a nakonfigurovat pro ni trasu UDR.  

> [!IMPORTANT]
> Trasy definované v trase UDR musí být dost konkrétní, aby měly přednost před všemi trasami inzerovanými konfigurací ExpressRoute. V předchozím příkladu se používá široký rozsah adres 0.0.0.0/0. Může nechtěně dojít k jeho potlačení z důvodu inzerování tras, které používají konkrétnější rozsahy adres.
>
> Služby App Service Environment nejsou podporované v konfiguracích ExpressRoute, které křížově inzerují trasy z cesty s veřejnými partnerskými uzly do cesty se soukromými partnerskými uzly. Konfigurace ExpressRoute s nakonfigurovanými veřejnými partnerskými uzly přijímají inzerci tras od Microsoftu. Tyto inzerce obsahují velkou sadu rozsahů adres Microsoft Azure. Pokud probíhá křížová inzerce rozsahů adres na cestě se soukromými partnerskými uzly, všechny odchozí síťové pakety z podsítě služby App Service Environment se směrují do místní síťové infrastruktury zákazníka. Služba App Service Environment tento tok sítí ve výchozím nastavení nepodporuje. Jedním řešením tohoto problému je ukončit křížovou inzerci tras z cesty s veřejnými partnerskými uzly do cesty se soukromými partnerskými uzly. Druhým řešením je povolit fungování služeb App Service Environment v konfiguraci s vynuceným tunelovým propojením.

![Přímí přístup k internetu][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>Konfigurace ignorování tras protokolu BGP v podsíti služby ASE ## 

V podsíti služby ASE můžete nakonfigurovat ignorování všech tras protokolu BGP.  Pokud je nakonfigurovaná tak, aby ignorovala trasy protokolu BGP, bude přístupový modul pro přístup k jeho závislostem moci bez problémů.  Budete však muset vytvořit trasy definované uživatelem, abyste svým aplikacím umožnili přístup k místním prostředkům.

Konfigurace ignorování tras protokolu BGP v podsíti služby ASE:

* Pokud jste to ještě neudělali, vytvořte trasu definovanou uživatelem a přiřaďte ji k vaší podsíti služby ASE.
* Na webu Azure Portal otevřete uživatelské rozhraní pro směrovací tabulku přiřazenou k vaší podsíti služby ASE.  Vyberte Konfigurace.  Nastavte šíření trasy brány virtuální sítě na zakázáno.  Klikněte na Uložit. Dokumentaci k vypnutí této možnosti najdete v dokumentu [Vytvoření směrovací tabulky][routetable].

Když nakonfigurujete podsíť pro pomocné služby tak, aby ignorovala všechny trasy protokolu BGP, aplikace už nebudou mít přístup k místním aplikacím. Pokud chcete vašim aplikacím povolit přístup k místním prostředkům, upravte UDR přiřazený k podsíti přihlašování a přidejte trasy pro místní rozsahy adres. Typ dalšího segmentu směrování by měl být nastavený na bránu virtuální sítě. 


## <a name="configure-your-ase-with-service-endpoints"></a>Konfigurace služby ASE s použitím koncových bodů služby ##

Pokud chcete směrovat veškerý odchozí provoz ze služby ASE kromě provozu směřujícího do SQL Azure a služby Azure Storage, proveďte následující kroky:

1. Vytvořte tabulku směrování a přiřaďte ji ke své podsíti služby ASE. Adresy odpovídající vaší oblasti najdete tady: [Adresy pro správu služby App Service Environment][management]. Vytvořte pro tyto adresy trasy s dalším segmentem směrování do internetu. Tyto trasy jsou potřeba, protože příchozí provoz správy App Service Environment musí odpovídat ze stejné adresy, na kterou se odeslal.   

2. Povolte koncové body služby s SQL Azure a službou Azure Storage v podsíti služby ASE.  Po dokončení tohoto kroku můžete ve své virtuální síti nakonfigurovat vynucené tunelování.

Podrobnosti o nasazení pomocného mechanismu pro přístup k šabloně najdete v [tématu vytvoření App Service Environment pomocí šablony][template].

Koncové body služby umožňují omezit přístup k víceklientským službám na sadu virtuálních sítí a podsítí Azure. Další informace o koncových bodech služby najdete v dokumentaci pro [koncové body služby pro virtuální síť][serviceendpoints]. 

Když pro prostředek povolíte koncové body služby, vytvoří se trasy s vyšší prioritou než všechny ostatní trasy. Pokud použijete koncové body služby se službou ASE s vynuceným tunelováním, nebude se vynucovat tunelování provozu správy SQL Azure a služby Azure Storage. Provoz ostatních závislostí služby ASE se bude vynuceně tunelovat a nesmí se ztratit, jinak služba ASE nebude správně fungovat.

Pokud jsou koncové body služby povolené v podsíti s instancí SQL Azure, musí mít koncové body služby povolené i všechny instance SQL Azure, ke kterým se z této podsítě připojuje. Pokud chcete ze stejné podsítě přistupovat k několika instancím SQL Azure, není možné povolit koncové body služby v jedné instanci SQL Azure a v jiné ne.  Služba Azure Storage se nechová stejně jako SQL Azure.  Když povolíte koncové body služby se službou Azure Storage, uzamknete přístup k danému prostředku z vaší podsítě, ale stále budete mít přístup k ostatním účtům služby Azure Storage, a to i v případě, že nemají povolené koncové body služby.  

Pokud konfigurujete vynucené tunelování s použitím zařízení síťového filtru, nezapomeňte, že služba ASE má závislosti mimo SQL Azure a službu Azure Storage. Pokud se těmto závislostem zablokuje provoz, nebude pomocné funkce fungovat správně.

![Vynucené tunelování s použitím koncových bodů služby][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>Přidání vlastních IP adres do brány firewall SQL Azure pro službu ASE ##

Pokud chcete tunelovat veškerý odchozí provoz ze služby ASE kromě provozu směřujícího do služby Azure Storage, proveďte následující kroky:

1. Vytvořte tabulku směrování a přiřaďte ji ke své podsíti služby ASE. Adresy odpovídající vaší oblasti najdete tady: [Adresy pro správu služby App Service Environment][management]. Vytvořte pro tyto adresy trasy s dalším segmentem směrování do internetu. Tyto trasy jsou potřeba, protože příchozí provoz správy App Service Environment musí odpovídat ze stejné adresy, na kterou se odeslal. 

2. Povolení koncových bodů služby se službou Azure Storage v podsíti služby ASE

3. Získejte adresy, které se použijí pro veškerý odchozí provoz z vaší služby App Service Environment do internetu. Pokud provoz směrujete do místní sítě, tyto adresy jsou vaše IP adresy překladu adres nebo brány. Pokud chcete směrovat odchozí přenosy služby App Service Environment přes virtuální síťové zařízení, výstupní adresou je veřejná IP adresa tohoto virtuálního síťového zařízení.

4. _Nastavení odchozích adres v existujícím App Service Environment:_ Přejít na resources.azure.com a přejít na předplatné/ \<subscription id> /ResourceGroups/ \<ase resource group> /providers/Microsoft.Web/hostingEnvironments/ \<ase name> . Uvidíte zápis JSON, který popisuje vaši službu App Service Environment. Zkontrolujte, jestli se nahoře píše **Čtení/zápis**. Vyberte **Edit** (Upravit). Posuňte se do dolní části. U položky **userWhitelistedIpRanges** změňte hodnotu **null** na podobnou hodnotu jako v následujícímu příkladu. Použijte adresy, které chcete nastavit jako výstupní rozsah adres. 

    ```json
    "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"]
    ```

   V horní části vyberte možnost **PUT**. Tato možnost spustí ve vaší službě App Service Environment operaci škálování a upraví bránu firewall.

_Vytvoření služby ASE s výstupními adresami:_ Postupujte podle pokynů v tématu [Vytvoření služby App Service Environment pomocí šablony][template] a stáhněte příslušnou šablonu.  V souboru azuredeploy.json upravte část resources (prostředky) a mimo blok properties (vlastnosti) do ní vložte řádek **userWhitelistedIpRanges** s vašimi hodnotami.

```json
"resources": [
    {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
            "name": "[parameters('aseName')]",
            "location": "[parameters('aseLocation')]",
            "ipSslAddressCount": 0,
            "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
            "dnsSuffix" : "[parameters('dnsSuffix')]",
            "virtualNetwork": {
                "Id": "[parameters('existingVnetResourceId')]",
                "Subnet": "[parameters('subnetName')]"
            },
            "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
    }
]
```

Díky těmto změnám se bude odesílat provoz do služby Azure Storage přímo ze služby ASE a umožní se přístup k SQL Azure i z jiných adres, než je virtuální IP adresa služby ASE.

   ![Vynucené tunelové propojení s SQL povolených][3]

## <a name="preventing-issues"></a>Předcházení problémům ##

Pokud dojde k přerušení komunikace mezi službou ASE a jejími závislostmi, služba ASE přejde do špatného stavu.  Pokud služba ASE zůstane ve špatném stavu příliš dlouho, zablokuje se. Pokud chcete zrušit zablokování služby ASE, postupujte podle pokynů na portálu služby ASE.

Kromě prostého přerušení komunikace můžete službu ASE nepříznivě ovlivnit zavedením příliš vysoké latence. K příliš vysoké latenci může docházet v případě, že je služba ASE příliš vzdálená od vaší místní sítě.  Příkladem příliš velké vzdálenosti může být oddělení místní sítě oceánem nebo kontinentem. Příčinou latence může být také zahlcení intranetu nebo omezení šířky odchozího pásma.


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
