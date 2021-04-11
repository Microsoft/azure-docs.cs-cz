---
title: 'Kurz: konfigurace partnerského vztahu pro okruh ExpressRoute-Azure Portal'
description: V tomto kurzu se dozvíte, jak vytvořit a zřídit soukromé partnerské vztahy s ExpressRoute a Microsoftem pomocí Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: c4f76a02a88b91c082106566ca236abe25d4ed88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567344"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>Kurz: vytvoření a úprava partnerského vztahu pro okruh ExpressRoute pomocí Azure Portal

V tomto kurzu se dozvíte, jak vytvořit a spravovat konfiguraci směrování pro Azure Resource Manager okruh ExpressRoute pomocí Azure Portal. Můžete také kontrolovat stav, aktualizovat nebo odstranit a zrušit zřízení partnerských vztahů pro okruh ExpressRoute. Pokud chcete pro práci s okruhem použít jinou metodu, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Veřejný partnerský vztah](about-public-peering.md)
> * [Video – privátní partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

Pro okruh ExpressRoute můžete nakonfigurovat privátní partnerské vztahy a partnerské vztahy Microsoftu (veřejný partnerský vztah Azure se pro nové okruhy už nepoužívá). Partnerské vztahy se dají konfigurovat v libovolném pořadí, ve kterém si zvolíte. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o doménách směrování a partnerských vztazích najdete v tématu věnovaném [doménám směrování ExpressRoute](expressroute-circuit-peerings.md). Informace o veřejném partnerském vztahu najdete v tématu [veřejné partnerské vztahy ExpressRoute](about-public-peering.md).

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Konfigurace, aktualizace a odstranění partnerského vztahu Microsoftu pro okruh
> - Konfigurace, aktualizace a odstranění privátního partnerského vztahu Azure pro okruh

## <a name="prerequisites"></a>Požadavky

* Než začnete s konfigurací, ujistěte se, že jste prošli následující stránky:
    * [Požadavky](expressroute-prerequisites.md) 
    * [Požadavky směrování](expressroute-routing.md)
    * [Pracovní postupy](expressroute-workflows.md)
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, postupujte podle pokynů pro [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a před pokračováním dodržujte okruh povolený vaším poskytovatelem připojení. Ke konfiguraci partnerských vztahů musí být okruh ExpressRoute ve stavu zřizování a povoleno. 
* Pokud máte v úmyslu použít sdílený klíč nebo hash MD5, nezapomeňte použít klávesu na obou stranách tunelu. Limit je maximálně 25 alfanumerických znaků. Speciální znaky se nepodporují. 

Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IPVPN, jako je MPLS), poskytovatel připojení nakonfiguruje a spravuje směrování za vás. 

> [!IMPORTANT]
> Aktuálně prostřednictvím portálu pro správu služeb neinzerujeme partnerské vztahy nakonfigurované poskytovateli služeb. Pracujeme na tom, aby tato možnost brzy fungovala. Před konfigurací partnerských vztahů protokolu BGP se poraďte se svým poskytovatelem služeb.
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Partnerský vztah Microsoftu

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute.

> [!IMPORTANT]
> Partnerské vztahy Microsoftu okruhů ExpressRoute, které byly nakonfigurované před 1. srpna 2017, budou mít všechny předpony služby inzerované prostřednictvím partnerského vztahu Microsoftu, a to i v případě, že nejsou definované filtry směrování. Partnerský vztah Microsoftu pro okruhy ExpressRoute, které jsou nakonfigurované na nebo od 1. srpna 2017, nebudou mít inzerované předpony, dokud není k okruhu připojen filtr tras. Další informace najdete v tématu [Konfigurace filtru tras pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. Nakonfigurujte okruh ExpressRoute. Než budete pokračovat, zkontrolujte **stav poskytovatele** a ujistěte se, že je okruh zcela zřízený poskytovatelem připojení.

   Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat poskytovatele připojení, aby pro vás povolil partnerský vztah Microsoftu. Nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte následujícími kroky.

   **Stav poskytovatele okruhu: není zřízené.**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned.png" alt-text="Snímek obrazovky, který zobrazuje stránku s přehledem pro ukázkový okruh ExpressRoute s červeným rámečkem, jehož stav poskytovatele není zřízený":::

   **Stav poskytovatele okruhu: zřízený**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned.png" alt-text="Snímek obrazovky, který zobrazuje stránku s přehledem pro ukázkový okruh ExpressRoute s červeným rámečkem, jehož stav poskytovatele je nastaven na zřízený":::

2. Nakonfigurujte partnerský vztah Microsoftu pro okruh. Než budete pokračovat, ujistěte se, že máte k dispozici následující informace.

   * Pár podsítí, které vlastníte a zaregistrovali v RIR/IRR. Pro primární propojení se použije jedna podsíť, zatímco druhá se použije pro sekundární propojení. Z každé z těchto podsítí přiřadíte směrovač první použitelná IP adresa, protože společnost Microsoft použije pro svůj směrovač druhou použitelnou IP adresu. Pro tuto dvojici podsítí máte tři možnosti:
       * IPv4: dvě podsítě/30. Musí se jednat o platné předpony veřejných IPv4 adres.
       * IPv6: dvě podsítě/126. Musí se jednat o platné veřejné předpony IPv6.
       * Obojí: dvě podsítě/30 a dvě podsítě/126.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. U primárních i sekundárních propojení musíte použít stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Inzerované předpony: zadáte seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud plánujete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.
   * **Volitelné –** Zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované pro partnerský vztah jako číslo, můžete zadat číslo AS, na které jsou registrované.
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * **Volitelné –** Hodnota hash MD5, pokud se rozhodnete ji použít.
1. Můžete vybrat partnerský vztah, který chcete nakonfigurovat, jak je znázorněno v následujícím příkladu. Vyberte řádek partnerského vztahu Microsoftu.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-microsoft-peering.png" alt-text="Vybrat řádek partnerského vztahu Microsoftu":::

4. Nakonfigurujte partnerský vztah Microsoftu. Po zadání všech parametrů **uložte** konfiguraci. Příklad konfigurace znázorňuje následující obrázek:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m-validation-needed.png" alt-text="Nakonfigurujte vyžadované ověřování partnerského vztahu Microsoftu.":::

> [!IMPORTANT]
> Microsoft ověří, jestli se vám v registru směrování Internetu přiřadí zadané "inzerované veřejné předpony" a "partnerské číslo ASN" (nebo číslo ASN zákazníka). Pokud získáváte veřejné předpony z jiné entity a pokud přiřazení není zaznamenané u registru směrování, automatické ověřování se nedokončí a bude vyžadovat ruční ověření. Pokud se automatické ověření nezdaří, zobrazí se zpráva "vyžadováno ověření". 
>
> Pokud se zobrazí zpráva požadovaná kontrola, shromážděte dokumenty, které zobrazují veřejné předpony, do vaší organizace, která je uvedená jako vlastník předpony v registru směrování, a odešlete tyto dokumenty pro ruční ověření otevřením lístku podpory. 
>

   Pokud se váš okruh dostane do stavu "vyžadováno ověření", je nutné otevřít lístek podpory, který zobrazí důkaz vlastnictví předpon pro náš tým podpory. Lístek podpory můžete otevřít přímo z portálu, jak je znázorněno v následujícím příkladu:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png" alt-text="Vyžadování ověření – lístek podpory":::

5. Po úspěšném přijetí konfigurace se zobrazí podobné informace jako na následujícím obrázku:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/microsoft-peering-validation-configured.png" alt-text="Stav partnerského vztahu: nakonfigurováno":::

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Zobrazení podrobností partnerského vztahu Microsoftu

Vlastnosti partnerského vztahu Microsoftu si můžete zobrazit tak, že vyberete řádek pro partnerský vztah.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Zobrazit vlastnosti partnerského vztahu Microsoftu":::

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Aktualizace konfigurace partnerského vztahu Microsoftu

Můžete vybrat řádek pro partnerský vztah, který chcete upravit, a pak upravit vlastnosti partnerského vztahu a uložit změny.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png" alt-text="Vybrat řádek partnerského vztahu":::

## <a name="azure-private-peering"></a><a name="private"></a>Privátní partnerský vztah s Azure

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci privátního partnerského vztahu Azure pro okruh ExpressRoute.

> [!IMPORTANT]
> Podpora protokolu IPv6 pro soukromý partnerský vztah je v současnosti v **Public Preview**. Pokud chcete připojit virtuální síť k okruhu ExpressRoute pomocí nakonfigurovaného privátního partnerského vztahu založeného na protokolu IPv6, ujistěte se, že je vaše virtuální síť duální Stack, a postupujte podle pokynů popsaných [tady](../virtual-network/ipv6-overview.md).
> 
> 

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Nakonfigurujte okruh ExpressRoute. Než budete pokračovat, ujistěte se, že je okruh poskytovatelem připojení plně zřízený. 

   Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat poskytovatele připojení, aby pro vás povolil privátní partnerské vztahy Azure. Nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud však poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte dalšími kroky.

   **Stav poskytovatele okruhu: není zřízené.**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-private.png" alt-text="Snímek obrazovky se stránkou s přehledem pro ukázkový okruh ExpressRoute se červeným rámečkem, který je nastavený na nezřízený":::

   **Stav poskytovatele okruhu: zřízený**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned-private-peering.png" alt-text="Snímek obrazovky se stránkou s přehledem pro ukázkový okruh ExpressRoute se červeným rámečkem, který má nastavenou možnost zřizování":::

2. Nakonfigurujte soukromý partnerský vztah Azure pro okruh. Než budete pokračovat v dalších krocích, ujistěte se, že máte následující položky:

   * Dvojice podsítí, které nejsou součástí žádného adresního prostoru rezervovaného pro virtuální sítě. Pro primární propojení se použije jedna podsíť, zatímco druhá se použije pro sekundární propojení. Z každé z těchto podsítí přiřadíte směrovač první použitelná IP adresa, protože společnost Microsoft použije pro svůj směrovač druhou použitelnou IP adresu. Pro tuto dvojici podsítí máte tři možnosti:
       * IPv4: dvě podsítě/30.
       * IPv6: dvě podsítě/126.
       * Obojí: dvě podsítě/30 a dvě podsítě/126.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. U primárních i sekundárních propojení musíte použít stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS s výjimkou čísla od 65515 do 65520 (včetně).
   * Při konfiguraci privátního partnerského vztahu je nutné inzerovat trasy z místního hraničního směrovače do Azure přes protokol BGP.
   * **Volitelné –** Hodnota hash MD5, pokud se rozhodnete ji použít.
3. Vyberte řádek privátního partnerského vztahu Azure, jak je znázorněno v následujícím příkladu:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-private-peering.png" alt-text="Výběr řádku privátního partnerského vztahu":::

4. Nakonfigurujte soukromý partnerský vztah. Po zadání všech parametrů **uložte** konfiguraci.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/private-peering-configuration.png" alt-text="Konfigurace privátního peeringu":::

5. Po úspěšném přijetí konfigurace se zobrazí něco podobného jako v následujícím příkladu:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/save-private-peering.png" alt-text="Uložený soukromý partnerský vztah":::

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Zobrazení podrobností soukromého partnerského vztahu Azure

Vlastnosti soukromého partnerského vztahu Azure můžete zobrazit výběrem partnerského vztahu.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Zobrazit vlastnosti privátního partnerského vztahu":::

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Můžete vybrat řádek pro partnerský vztah a upravit vlastnosti partnerského vztahu. Po aktualizaci uložte změny.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/update-private-peering.png" alt-text="Aktualizace privátního partnerského vztahu":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu Microsoftu můžete odebrat tak, že kliknete pravým tlačítkem na partnerský vztah a vyberete **Odstranit** , jak je znázorněno na následujícím obrázku:

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-microsoft-peering.png" alt-text="Odstranit partnerský vztah Microsoftu":::

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci privátního partnerského vztahu můžete odebrat tak, že kliknete pravým tlačítkem na partnerský vztah a vyberete **Odstranit** , jak je znázorněno na následujícím obrázku:

> [!WARNING]
> Před spuštěním této operace je nutné zajistit, aby byly odebrány všechny virtuální sítě a připojení ExpressRoute Global Reach. 
> 

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-private-peering.png" alt-text="Odstranit soukromý partnerský vztah":::

## <a name="next-steps"></a>Další kroky

Po dokončení konfigurace privátního partnerského vztahu Azure můžete vytvořit bránu ExpressRoute pro propojení virtuální sítě s okruhem. 

> [!div class="nextstepaction"]
> [Konfigurace brány virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)