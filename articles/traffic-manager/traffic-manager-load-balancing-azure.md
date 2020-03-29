---
title: Používání služeb vyrovnávání zatížení v Azure | Dokumenty společnosti Microsoft
description: 'Tento kurz ukazuje, jak vytvořit scénář pomocí portfolia vyrovnávání zatížení Azure: Traffic Manager, Aplikační brána a Nástroje pro vyrovnávání zatížení.'
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: rohink
ms.openlocfilehash: b77248813463f51d4bd2c5186e421aec43ffaf52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939212"
---
# <a name="using-load-balancing-services-in-azure"></a>Použití služeb pro vyrovnávání zatížení v Azure

## <a name="introduction"></a>Úvod

Microsoft Azure poskytuje několik služeb pro správu distribuce síťového provozu a vyrovnávání zatížení. Tyto služby můžete použít jednotlivě nebo kombinovat jejich metody, v závislosti na vašich potřebách, k vytvoření optimálního řešení.

V tomto kurzu nejprve definujeme případ použití zákazníka a uvidíme, jak ho lze provést robustnější a výkonnější pomocí následujícího portfolia vyrovnávání zatížení Azure: Traffic Manager, Application Gateway a Load Balancer. Pak poskytujeme podrobné pokyny pro vytvoření nasazení, které je geograficky redundantní, distribuuje provoz do virtuálních počítačů a pomáhá vám spravovat různé typy požadavků.

Na koncepční úrovni každá z těchto služeb hraje odlišnou roli v hierarchii vyrovnávání zatížení.

* **Traffic Manager** poskytuje globální vyrovnávání zatížení DNS. Podívá se na příchozí požadavky DNS a reaguje s koncovým bodem v pořádku v souladu se zásadami směrování, které zákazník vybral. Možnosti pro metody směrování jsou:
  * Směrování výkonu k odeslání žadatele do nejbližšího koncového bodu z hlediska latence.
  * Priorita směrování směrovat veškerý provoz do koncového bodu, s ostatními koncovými body jako záloha.
  * Vážené kruhové dotazování směrování, které distribuuje provoz na základě vážení, který je přiřazen ke každému koncovému bodu.
  * Směrování založené na zeměpisné poloze k distribuci provozu do koncových bodů aplikace na základě geografické polohy uživatele.
  * Směrování založené na podsíti pro distribuci přenosů do koncových bodů aplikace na základě podsítě (rozsah IP adres) uživatele.
  * Směrování s více hodnotami, které umožňují odesílat adresy IP více než jednoho koncového bodu aplikace v jedné odpovědi DNS.

  Klient se připojí přímo ke koncovému bodu vrácenému Traffic Manager. Azure Traffic Manager zjistí, když koncový bod není v pořádku a pak přesměruje klienty na jinou instanci v pořádku. Další informace o službě najdete v [dokumentaci](traffic-manager-overview.md) k Azure Traffic Manageru.
* **Aplikační brána** poskytuje řadič pro doručování aplikací (ADC) jako službu, která nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaši aplikaci. Umožňuje zákazníkům optimalizovat produktivitu webové farmy převedením ukončení SSL náročné na procesor na aplikační bránu. Mezi další možnosti směrování vrstvy 7 patří distribuce příchozích přenosů s každým dotazováním, spřažení relací na základě souborů cookie, směrování založené na cestě url a možnost hostovat více webů za jednou aplikační bránou. Aplikační bránu lze nakonfigurovat jako internetovou bránu, bránu pouze pro interní nebo kombinaci obou. Aplikační brána je plně spravovaná, škálovatelná a vysoce dostupná. Nabízí celou řadu možností diagnostiky a protokolování, které zlepšují správu.
* **Nástroj pro vyrovnávání zatížení** je nedílnou součástí zásobníku Azure SDN a poskytuje vysoce výkonné služby vyrovnávání zatížení vrstvy 4 s nízkou latencí pro všechny protokoly UDP a TCP. Spravuje příchozí a odchozí připojení. Pokud chcete spravovat dostupnost služby, můžete nakonfigurovat veřejné a interní koncové body s vyrovnáváním zatížení a definovat pravidla pro mapování příchozích připojení na cíle v backendovém fondu s využitím možností monitorování stavu protokolů TCP a HTTP.

## <a name="scenario"></a>Scénář

V tomto příkladovém scénáři používáme jednoduchý web, který zobrazuje dva typy obsahu: obrázky a dynamicky vykreslené webové stránky. Web musí být geograficky redundantní a měl by sloužit svým uživatelům z nejbližšího umístění (nejnižší latence). Vývojář aplikace se rozhodl, že všechny adresy URL, které odpovídají vzoru /images/* jsou obsluhovány z vyhrazeného fondu virtuálních stránek, které se liší od zbytku webové farmy.

Kromě toho výchozí fond virtuálních her, který slouží dynamický obsah potřebuje mluvit do back-end databáze, která je hostovaná v clusteru s vysokou dostupností. Celé nasazení se nastavuje prostřednictvím Správce prostředků Azure.

Použití Traffic Manager, Application Gateway a Load Balancer umožňuje tento web k dosažení těchto cílů návrhu:

* **Multigeografická redundance**: Pokud jedna oblast přejde dolů, Traffic Manager směruje provoz bez problémů do nejbližší oblasti bez zásahu vlastníka aplikace.
* **Snížená latence**: Vzhledem k tomu, že Traffic Manager automaticky přesměruje zákazníka do nejbližší oblasti, zákazník dojde k nižší latenci při požadování obsahu webové stránky.
* **Nezávislé škálovatelnost**: Vzhledem k tomu, že zatížení webové aplikace je odděleno podle typu obsahu, vlastník aplikace můžete škálovat úlohy požadavku nezávisle na sobě. Aplikační brána zajišťuje, že provoz je směrován do správné fondy na základě zadaných pravidel a stavu aplikace.
* **Vnitřní vyrovnávání zatížení**: Vzhledem k tomu, že vykladač zatížení je před clusteru s vysokou dostupností, pouze aktivní a v pořádku koncový bod pro databázi je vystavena aplikaci. Správce databáze navíc můžete optimalizovat zatížení distribucí aktivní a pasivní repliky v rámci clusteru nezávisle na front-endové aplikace. Služba vyrovnávání zatížení poskytuje připojení do clusteru s vysokou dostupností a zajišťuje, že požadavky na připojení přijímají pouze databáze v pořádku.

Následující diagram znázorňuje architekturu tohoto scénáře:

![Diagram architektury vyrovnávání zatížení](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Tento příklad je pouze jedním z mnoha možných konfigurací služby vyrovnávání zatížení, které Azure nabízí. Traffic Manager, Aplikační brána a Nástroje pro vyrovnávání zatížení lze smíchat a přizpůsobit tak, aby co nejlépe vyhovovaly vašim potřebám vyrovnávání zatížení. Například pokud ssl přepětí nebo vrstva 7 zpracování není nutné, vyrovnávání zatížení lze použít místo aplikační brány.

## <a name="setting-up-the-load-balancing-stack"></a>Nastavení zásobníku vyrovnávání zatížení

### <a name="step-1-create-a-traffic-manager-profile"></a>Krok 1: Vytvoření profilu Traffic Managera

1. Na webu Azure Portal klikněte na Vytvořit**profil** > **správce provozu sítě** > pro **sítě** > **vytvořit**.
2. Zadejte tyto základní informace:

   * **Název**: Pojmenujte profil Traffic Manager u předpony DNS.
   * **Metoda směrování**: Vyberte zásadu metody směrování provozu. Další informace o metodách naleznete v tématu [O metodách směrování provozu traffic manageru](traffic-manager-routing-methods.md).
   * **Předplatné**: Vyberte předplatné, které obsahuje profil.
   * **Skupina prostředků**: Vyberte skupinu prostředků, která obsahuje profil. Může se jedná o novou nebo existující skupinu prostředků.
   * **Umístění skupiny prostředků**: Služba Traffic Manager je globální a není vázána na umístění. Je však nutné zadat oblast pro skupinu, ve které se nacházejí metadata přidružená k profilu Traffic Manageru. Toto umístění nemá žádný vliv na dostupnost za běhu profilu.

3. Kliknutím na **Vytvořit** vygenerujte profil Traffic Manageru.

   ![Okno "Vytvořit správce provozu"](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Krok 2: Vytvoření aplikačních bran

1. Na portálu Azure klikněte v levém podokně na Vytvořit**Networking** > **síťovou aplikační** **bránu.** > 
2. Zadejte následující základní informace o bráně aplikace:

   * **Název**: Název aplikační brány.
   * **Velikost skladové položky**: Velikost brány aplikace, která je k dispozici jako malá, střední nebo velká.
   * **Počet instancí**: Počet instancí, hodnota od 2 do 10.
   * **Skupina prostředků**: Skupina prostředků, která obsahuje aplikační bránu. Může se jedná o existující skupinu prostředků nebo novou.
   * **Umístění:** Oblast pro aplikační bránu, která je ve stejném umístění jako skupina prostředků. Umístění je důležité, protože virtuální síť a veřejná IP adresa musí být ve stejném umístění jako brána.
3. Klikněte na tlačítko **OK**.
4. Definujte konfigurace virtuální sítě, podsítě, front-endové IP adresy a naslouchací procespro aplikační bránu. V tomto scénáři je adresa IP front-endu **Veřejná**, což umožňuje její pozdější přidání jako koncový bod do profilu traffic manageru.
5. Nakonfigurujte naslouchací proces pomocí jedné z následujících možností:
    * Pokud používáte protokol HTTP, není nic konfigurovat. Klikněte na tlačítko **OK**.
    * Pokud používáte protokol HTTPS, je vyžadována další konfigurace. Viz [Vytvoření aplikační brány](../application-gateway/application-gateway-create-gateway-portal.md), počínaje krokem 9. Po dokončení konfigurace klepněte na tlačítko **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurace směrování adres URL pro aplikační brány

Když zvolíte fond back-end, brána aplikace, která je nakonfigurována s pravidlem založeným na cestě, převezme kromě distribuce kruhového dotazování vzor cesty adresy URL požadavku. V tomto scénáři přidáváme pravidlo založené na cestě k přímé\*libovolné adrese URL s "/images/ " do fondu serveru bitové kopie. Další informace o konfiguraci směrování založené na cestě k adrese URL pro aplikační bránu naleznete v článku [Vytvoření pravidla založeného na cestě pro aplikační bránu](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagram webové vrstvy aplikační brány](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Ve skupině prostředků přejděte na instanci aplikační brány, kterou jste vytvořili v předchozí části.
2. V části **Nastavení**vyberte **Fondy back-endu**a pak vyberte **Přidat,** chcete přidat virtuální počítače, které chcete přidružit k back-endovým fondům webové vrstvy.
3. Zadejte název back-endového fondu a všechny IP adresy počítačů, které jsou ve fondu umístěny. V tomto scénáři jsme připojení dvou back-end server fondy virtuálních počítačů.

   ![Aplikační brána "Přidat back-endový fond"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. V části **Nastavení** aplikační brány vyberte **Pravidla**a klepnutím na tlačítko **Cesta na základě** přidáte pravidlo.

   ![Tlačítko Pravidla brány aplikace "Cesta založená"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Nakonfigurujte pravidlo poskytnutím následujících informací.

   Základní nastavení:

   + **Název**: Popisný název pravidla, které je přístupné na portálu.
   + **Naslouchací proces**: Naslouchací proces, který se používá pro pravidlo.
   + **Výchozí fond back-endu**: Fond back-end, který se má použít s výchozím pravidlem.
   + **Výchozí nastavení protokolu HTTP**: Nastavení protokolu HTTP, které má být použito s výchozím pravidlem.

   Pravidla založená na cestě:

   + **Název**: Popisný název pravidla založeného na cestě.
   + **Cesty**: Pravidlo cesty, které se používá pro předávání provozu.
   + **Back-end fond**: Back-end fondu, který má být použit s tímto pravidlem.
   + **Nastavení protokolu HTTP**: Nastavení protokolu HTTP, které má být použito s tímto pravidlem.

   > [!IMPORTANT]
   > Cesty: Platné cesty musí začínat na "/". Zástupný znak\*" " je povolen pouze na konci. Platné příklady jsou /xyz,\*/xyz nebo\*/xyz/ .

   ![Okno "Přidat pravidlo založené na cestě" aplikace](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Krok 3: Přidání bran aplikací do koncových bodů Traffic Manageru

V tomto scénáři Traffic Manager je připojen k aplikační brány (jak je nakonfigurováno v předchozích krocích), které jsou umístěny v různých oblastech. Teď, když jsou nakonfigurované aplikační brány, je dalším krokem jejich připojení k profilu Traffic Manageru.

1. Otevřete svůj profil Traffic Manageru. Chcete-li tak učinit, vyhledejte ve skupině prostředků nebo vyhledejte název profilu Traffic Manageru ze **všech zdrojů**.
2. V levém podokně vyberte **Koncové body**a pak kliknutím na **Přidat** přidejte koncový bod.

   ![Tlačítko "Přidat" koncové body Správce provozu](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Vytvořte koncový bod zadáním následujících informací:

   * **Typ**: Vyberte typ koncového bodu pro vyrovnávání zatížení. V tomto scénáři vyberte **koncový bod Azure,** protože ho připojujeme k instancím aplikační brány, které byly dříve nakonfigurované.
   * **Název**: Zadejte název koncového bodu.
   * **Cílový typ prostředku**: Vyberte **veřejnou IP adresu** a potom v části Cílový **prostředek**vyberte veřejnou IP adresu aplikační brány, která byla dříve nakonfigurovaná.

   ![Traffic Manager "Přidat koncový bod"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Nyní můžete nastavení otestovat tak, že k němu budete přistupovat pomocí služby DNS profilu traffic manageru (v tomto příkladu: TrafficManagerScenario.trafficmanager.net). Můžete znovu odesílat požadavky, vyvolat nebo zmítat virtuální počítače a webové servery, které byly vytvořeny v různých oblastech, a změnit nastavení profilu Traffic Manageru a otestovat nastavení.

### <a name="step-4-create-a-load-balancer"></a>Krok 4: Vytvoření zátěžového reliéru

V tomto scénáři balancer distribuuje připojení z webové vrstvy do databází v rámci clusteru s vysokou dostupností.

Pokud váš databázový cluster s vysokou dostupností používá SQL Server AlwaysOn, podívejte se na [na konfigurovat jeden nebo více vždy na posluchače skupiny dostupnosti](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) pro podrobné pokyny.

Další informace o konfiguraci interního zařízení pro vyrovnávání zatížení najdete [v tématu Vytvoření interního zařízení pro vyrovnávání zatížení na webu Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. Na portálu Azure klikněte v levém podokně na Vytvořit**nástroje** > **pro vyrovnávání zatížení** **sítě prostředků** > .
2. Zvolte název pro vyrovnávání zatížení.
3. Nastavte **typ** na **interní**a zvolte příslušnou virtuální síť a podsíť pro nástroj pro vyrovnávání zatížení, ve kterých se má nastolit.
4. V části **Přiřazení IP adresy**vyberte možnost **Dynamická** nebo **Statická**.
5. V části **Skupina prostředků**zvolte skupinu prostředků pro vyvyčažatele zatížení.
6. V části **Umístění**zvolte příslušnou oblast pro vykladač zatížení.
7. Kliknutím na **Vytvořit** vygenerujte vykreslovač zatížení.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Připojení back-endové databázové vrstvy k systému vyrovnávání zatížení

1. Ve skupině prostředků vyhledejte vyvyčován zatížení, které bylo vytvořeno v předchozích krocích.
2. V části **Nastavení**klikněte na **Fondy back-endů**a potom kliknutím na **Přidat** přidejte fond back-endu.

   ![Vykladač zatížení "Přidat back-endový fond"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Zadejte název back-endového fondu.
4. Přidejte do back-endového fondu buď jednotlivé počítače, nebo sadu dostupnosti.

#### <a name="configure-a-probe"></a>Konfigurace sondy

1. V zařízení pro vyrovnávání zatížení vyberte v části **Nastavení** **možnost Prodbe**a klepnutím na tlačítko **Přidat** přidejte sondu.

   ![Vyrovnávání zatížení "Přidat sondu"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Zadejte název sondy.
3. Vyberte **protokol** pro sondu. Pro databázi můžete chtít sondu TCP spíše než sondu HTTP. Další informace o sondách vykladačů zatížení naleznete v části [Principy sond pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md).
4. Zadejte **port** databáze, který má být použit pro přístup k sondě.
5. V části **Interval**určete, jak často má být aplikace sondut.
6. V části **Prahová hodnota není v pořádku**zadejte počet selhání průběžné sondy, ke kterým musí dojít, aby byl virtuální virtuální virtuální soud back-end považován za nefunkční.
7. Chcete-li sondu vytvořit, klepněte na tlačítko **OK.**

#### <a name="configure-the-load-balancing-rules"></a>Konfigurace pravidel vyrovnávání zatížení

1. V **části Nastavení** zařízení pro vyrovnávání zatížení vyberte Pravidla **vyrovnávání zatížení**a kliknutím na **Přidat** vytvořte pravidlo.
2. Zadejte **název** pravidla vyrovnávání zatížení.
3. Zvolte **front-endovou IP adresu** vykladaču zatížení, **protokolu**a **portu**.
4. V části **Back-end port**zadejte port, který má být použit v back-endfondu.
5. Vyberte **back-endový fond** a **probe,** které byly vytvořeny v předchozích krocích použít pravidlo.
6. V **části Trvalosti relace**zvolte, jak mají relace přetrvávat.
7. V části **Nečinnosti časové limity**, zadejte počet minut před časový limit nečinnosti.
8. V části **Plovoucí IP**vyberte **buď Zakázanou** nebo **Povolenou**.
9. Kliknutím na **OK** vytvořte pravidlo.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Krok 5: Připojení virtuálních zařízení na webu k balanceru zatížení

Teď nakonfigurujeme ip adresu a front-end port vyvažovače zatížení v aplikacích, které jsou spuštěny na virtuálních počítačích webové vrstvy pro všechna připojení k databázi. Tato konfigurace je specifická pro aplikace, které běží na těchto virtuálních počítačích. Chcete-li nakonfigurovat cílovou adresu IP a port, podívejte se do dokumentace k aplikaci. Pokud chcete najít IP adresu front-endu, přejděte na portálu Azure do fondu IP adres front-endu v **nastavení vyrovnávání zatížení**.

![Navigační podokno Vykladatel/dolina zatížení "Frontend OVÝ fond IP poolu"](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Další kroky

* [Přehled služby Traffic Manager](traffic-manager-overview.md)
* [Přehled služby Application Gateway](../application-gateway/application-gateway-introduction.md)
* [Azure Load Balancer – přehled](../load-balancer/load-balancer-overview.md)
