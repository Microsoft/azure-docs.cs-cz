---
title: Použití služby Vyrovnávání zatížení v Azure | Microsoft Docs
description: 'V tomto kurzu se dozvíte, jak vytvořit scénář pomocí Azure portfolií Vyrovnávání zatížení: Traffic Manager, aplikační brány a vyrovnávání zatížení.'
services: traffic-manager
documentationcenter: ''
author: liumichelle
manager: vitinnan
editor: ''
ms.assetid: f89be3be-a16f-4d47-bcae-db2ab72ade17
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
ms.openlocfilehash: 86867a9d6d2c43e6505b1a06672546a017172bfe
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29401102"
---
# <a name="using-load-balancing-services-in-azure"></a>Použití služby Vyrovnávání zatížení v Azure

## <a name="introduction"></a>Úvod

Microsoft Azure poskytuje několik služeb pro správu, jak je distribuován síťový provoz a skupinu s vyrovnáváním zatížení. Můžete použít tyto služby samostatně nebo v kombinaci své metody podle svých potřeb, k vytvoření optimální řešení.

V tomto kurzu jsme nejprve definovat případu použití zákazníka a v tématu jak ji můžete provést robustnější a původce pomocí následující Azure Vyrovnávání zatížení portfolií: Traffic Manager, aplikační brány a vyrovnávání zatížení. Jsme potom poskytují podrobné pokyny pro vytvoření nasazení, které je geograficky redundantní distribuuje provoz na virtuální počítače a vám pomůže spravovat různé typy požadavků.

Koncepční úrovni každý z těchto služeb hraje důležitou roli v hierarchii Vyrovnávání zatížení.

* **Správce provozu** poskytuje globální DNS Vyrovnávání zatížení. Ho vypadá na příchozí požadavky na DNS a odpoví funkční koncový bod, v souladu s zásady směrování, zvolené zákazníka. Možnosti pro směrování metody jsou následující:
  * Výkon směrování k odeslání do nejbližší koncového bodu z hlediska latence žadatel.
  * Priorita směrování do směrovat všechen provoz na koncový bod, s další koncové body zálohu.
  * Vážené kruhové dotazování směrování, která distribuuje provoz podle vyvážení, která je přiřazena k každý koncový bod.

  Připojení klienta přímo do tohoto koncového bodu. Azure Traffic Manager zjistí, že koncový bod není v pořádku a pak přesměruje klienty k jiné instanci v pořádku. Odkazovat na [dokumentace Azure Traffic Manager](traffic-manager-overview.md) Další informace o službě.
* **Aplikační brána** poskytuje aplikace doručení řadiče (ADC) jako služba nabízí různé vrstvy 7 Vyrovnávání zatížení možnosti pro vaši aplikaci. To umožňuje zákazníkům za účelem optimalizace webové farmy produktivitu přesměrováním ukončení protokolu SSL náročná na prostředky procesoru aplikační brány. Další možnosti směrování vrstvy 7 zahrnují kruhového dotazování distribuce příchozí provoz, spřažení na základě souboru cookie relace, na základě cestu směrování adres URL a umožňuje hostování více webů za bránu jednu aplikaci. Application Gateway můžete nakonfigurovat jako bránu internetové brány, bránu pouze interní nebo obojí. Application Gateway je plně Azure spravované, škálovatelné a vysoce dostupné. Nabízí celou řadu možností diagnostiky a protokolování, které zlepšují správu.
* **Nástroj pro vyrovnávání zatížení** je nedílnou součástí zásobníku Azure SDN, který poskytuje vysoce výkonné, nízkou latencí 4 vrstvy Vyrovnávání zatížení služby pro všechny protokoly UDP a TCP. Spravuje příchozí a odchozí připojení. Můžete nakonfigurovat veřejný a interní Vyrovnávání zatížení sítě koncových bodů a definování pravidel pro mapování příchozí připojení na fond back-end cíle pomocí TCP a HTTP zjišťování stavu možností pro správu dostupnost služeb.

## <a name="scenario"></a>Scénář

V tomto ukázkovém scénáři používáme jednoduché web, který má dva typy obsahu: bitové kopie a dynamicky vykreslené webové stránky. Na webu musí být geograficky redundantní a svým uživatelům z nejbližšího (nejnižší latenci) by měla sloužit umístění k nim. Vývojář aplikace se rozhodla, že všechny adresy URL, které odpovídají vzor/Image / * ze fond virtuálních počítačů, které se liší od zbytku webové farmy.

Výchozí fond virtuálních počítačů obsluhující dynamický obsah se navíc musí ke komunikaci s back-end databázi, která je hostovaná v clusteru s vysokou dostupností. Celé nasazení je nastavit prostřednictvím Správce Azure Resource Manager.

Tento web k dosažení těchto cílů návrhu pomocí Traffic Manager, aplikační brány a nástroj pro vyrovnávání zatížení umožňuje:

* **Více geografická redundance**: Pokud jedné oblasti přestane fungovat, Traffic Manager směruje provoz bezproblémově nejbližší oblast bez jakéhokoli zásahu od vlastníka aplikace.
* **Nižší latenci**: protože Traffic Manager automaticky přesměruje zákazníka na nejbližší oblast, zákazník vyskytne nižší latenci žádosti o webovou stránku obsahu.
* **Nezávislé škálovatelnost**: protože zatížení webové aplikace je oddělená typ obsahu, vlastník aplikace můžete škálovat nezávisle na sobě navzájem žádost o úlohy. Aplikační brány zajistí, že provoz se směruje na pravém fondů na základě zadaného pravidla a stavu aplikace.
* **Vyrovnávání zatížení pro vnitřní**: protože Vyrovnávání zatížení je před clusteru vysokou dostupnost, pouze aktivní a pořádku koncový bod pro databázi má přístup k aplikaci. Kromě toho správce databáze můžete optimalizovat zatížení distribuci aktivní a pasivní repliky v clusteru, které jsou nezávislé na aplikace front-endu. Nástroj pro vyrovnávání zatížení poskytuje připojení do clusteru s vysokou dostupností a zajistí, že pouze v pořádku databáze přijímat žádosti o připojení.

Následující diagram znázorňuje architekturu tento scénář:

![Diagram Vyrovnávání zatížení architektura](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> V tomto příkladu je pouze jeden z mnoha možné konfigurace služby Vyrovnávání zatížení, které Azure nabízí. Traffic Manager, aplikační brány a vyrovnávání zatížení můžete kombinovat a porovnáváno podle potřeb Vyrovnávání zatížení. Například pokud přesměrování zpracování SSL nebo zpracování vrstvy 7 není nezbytné, nástroj pro vyrovnávání zatížení můžete použít místo Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>Nastavení služby Vyrovnávání zatížení zásobníku

### <a name="step-1-create-a-traffic-manager-profile"></a>Krok 1: Vytvoření profilu Traffic Manageru

1. Na portálu Azure klikněte na tlačítko **vytvořit prostředek** > **sítě** > **profil služby Traffic Manager**  >   **Vytvoření**.
2. Zadejte následující informace:

  * **Název**: udělte vašeho profilu Traffic Manageru DNS název předpony.
  * **Metody směrování**: vyberte zásadu, metoda směrování provozu. Další informace o metodách v tématu [metodách směrování provozu Traffic Manager](traffic-manager-routing-methods.md).
  * **Předplatné**: Vyberte odběr, který obsahuje profil.
  * **Skupina prostředků**: Vyberte skupinu prostředků, který obsahuje profil. Může být nový nebo stávající skupiny prostředků.
  * **Umístění skupiny prostředků**: Služba Traffic Manager je globální a není vázaná na umístění. Však musíte zadat oblast pro skupinu, které se budou metadata spojená s profilem Traffic Manager nachází. Toto umístění nemá žádný vliv na běhovou dostupnost profilu.

3. Klikněte na tlačítko **vytvořit** ke generování profil služby Traffic Manager.

  ![Okno "Vytvořit Traffic Manager"](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Krok 2: Vytvoření služby application Gateway

1. Na portálu Azure, v levém podokně klikněte na tlačítko **vytvořit prostředek** > **sítě** > **Application Gateway**.
2. Zadejte následující informace o službě application gateway:

  * **Název**: název služby application gateway.
  * **Velikost SKU**: velikost aplikační bránu, k dispozici jako malé, střední nebo velké.
  * **Instance počet**: počet instancí, hodnotu v rozmezí 2 až 10.
  * **Skupina prostředků**: skupinu prostředků, která obsahuje službu application gateway. Může být existující skupinu prostředků nebo novou.
  * **Umístění**: oblasti pro službu application gateway, což je stejné umístění jako pro skupinu prostředků. Umístění je důležité, protože virtuální síť a veřejnou IP adresu musí být ve stejném umístění jako brána.
3. Klikněte na **OK**.
4. Zadejte virtuální sítě, podsítě, front-end IP adresu a konfigurace naslouchacího procesu pro službu application gateway. V tomto scénáři front-end IP adresa je **veřejné**, což umožňuje přidat jako koncový bod profilu Správce provozu později.
5. Naslouchací proces nakonfigurujte s jedním z následujících možností:
    * Pokud používáte protokol HTTP, není nic konfigurovat. Klikněte na **OK**.
    * Pokud použijete protokol HTTPS, další konfigurace se nevyžaduje. Odkazovat na [vytvoření služby application gateway](../application-gateway/application-gateway-create-gateway-portal.md)začínající kroku 9. Po dokončení konfigurace klikněte na tlačítko **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurace adresy URL směrování pro application Gateway

Když zvolíte fond back-end, aplikační bránu, která je konfigurováno pravidlo na základě cesty trvá vzorek cesty adresy URL žádosti kromě distribučních kruhového dotazování. V tomto scénáři přidáváme pravidla na základě cesty k přímé libovolná adresa URL s "/images/\*" do fondu serverů bitové kopie. Další informace o konfiguraci adresy URL na základě cestu směrování pro aplikační bránu, najdete v části [vytvoření pravidla na základě cesty pro aplikační bránu](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagram webová vrstva brány aplikace](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Od vaší skupiny prostředků přejděte na instanci služby application gateway, který jste vytvořili v předchozí části.
2. V části **nastavení**, vyberte **back-endové fondy**a potom vyberte **přidat** přidat virtuální počítače, které chcete přidružit k back endové fondy webovou vrstvu.
3. Zadejte název fondu back-end a všechny IP adresy počítačů, které jsou umístěny ve fondu. V tomto scénáři se připojujete, dvě back-end serverů fondy virtuálních počítačů.

  ![Aplikační brány "Přidat fond back-end."](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. V části **nastavení** aplikační brány, vyberte **pravidla**a klikněte **na základě cest** tlačítko Přidat pravidlo.

  ![Tlačítko "Cesta založená" pravidla brány aplikace](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Nakonfigurujte pravidlo tím, že poskytuje následující informace.

   Základní nastavení:

   + **Název**: popisný název pravidla, která je přístupná na portálu.
   + **Naslouchací proces**: naslouchací proces, který se používá pro pravidlo.
   + **Výchozí fond back-end**: fondu back-end, který se má použít s výchozí pravidlo.
   + **Výchozí nastavení HTTP**: nastavení protokolu HTTP pro použití s výchozí pravidlo.

   Pravidla na základě cesty:

   + **Název**: popisný název pravidla na základě cesty.
   + **Cesty**: pravidlo cesty, který se používá pro předávání dál provoz.
   + **Fond back-end**: fondu back-end, který se má použít s tímto pravidlem.
   + **Nastavení HTTP**: nastavení protokolu HTTP, který se má použít s tímto pravidlem.

   > [!IMPORTANT]
   > Cesty: Platná cesta musí začínat znakem "/". Zástupný znak "\*" je povolena pouze na konci. Platnými hodnotami jsou /xyz, /xyz\*, nebo /xyz/\*.

   ![Okna "Přidat na základě cesty pravidlo" brány aplikací](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Krok 3: Přidání application Gateway ke koncovým bodům Traffic Manager

V tomto scénáři Traffic Manager je připojený k application Gateway (jak je nakonfigurované v předchozích krocích), které se nacházejí v různých oblastech. Teď, když jsou nakonfigurované application Gateway, dalším krokem je pro připojení k vašeho profilu Traffic Manageru.

1. Otevřete svůj profil Traffic Manageru. Uděláte to tak, podívejte se na skupinu prostředků nebo vyhledejte název profilu Traffic Manageru z **všechny prostředky**.
2. V levém podokně vyberte **koncové body**a potom klikněte na **přidat** k přidání koncového bodu.

  ![Správce provozu koncové body "Přidat" tlačítko](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Vytvoření koncového bodu tak, že zadáte následující informace:

  * **Typ**: Vyberte typ koncového bodu Vyrovnávání zatížení. V tomto scénáři vyberte **koncového bodu Azure** vzhledem k tomu, že se připojujete ke instancí brány aplikace, které byly dříve nakonfigurovány.
  * **Název**: Zadejte název koncového bodu.
  * **Cíl typ prostředku**: vyberte **veřejnou IP adresu** a pak v části **cíle prostředků**, vyberte veřejné IP adresy aplikační brány, který byl dříve nakonfigurován.

   ![Správce provozu "Přidat koncový bod"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Nyní můžete otestovat vašeho nastavení přístup s DNS vašeho profilu Traffic Manageru (v tomto příkladu: TrafficManagerScenario.trafficmanager.net). Můžete znovu odeslal požadavky, zprovoznit nebo převést virtuální počítače a webové servery, které byly vytvořeny v různých oblastech a změnit nastavení profilu Traffic Manageru k testování vašeho nastavení.

### <a name="step-4-create-a-load-balancer"></a>Krok 4: Vytvoření služby Vyrovnávání zatížení

Nástroj pro vyrovnávání zatížení v tomto scénáři distribuuje připojení z webovou vrstvu k databází v rámci clusteru s vysokou dostupností.

Pokud váš cluster vysokou dostupnost databáze používá SQL Server AlwaysOn, podívejte se na [nakonfigurovat jeden nebo více vždy na dostupnosti naslouchací procesy skupiny](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) podrobné pokyny.

Další informace o konfiguraci Vyrovnávání zatížení interní najdete v tématu [vytvořit interní nástroj na webu Azure portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. Na portálu Azure, v levém podokně klikněte na tlačítko **vytvořit prostředek** > **sítě** > **nástroj pro vyrovnávání zatížení**.
2. Zvolte název pro nástroj pro vyrovnávání zatížení.
3. Nastavte **typ** k **interní**a vyberte odpovídající virtuální síť a podsíť pro nástroje pro vyrovnávání zatížení jsou umístěny v.
4. V části **přiřazení IP adresy**, vyberte buď **dynamické** nebo **statické**.
5. V části **skupiny prostředků**, zvolte skupinu prostředků, nástroje pro vyrovnávání zatížení.
6. V části **umístění**, vyberte příslušnou oblast, nástroj pro vyrovnávání zatížení.
7. Klikněte na tlačítko **vytvořit** ke generování nástroje pro vyrovnávání zatížení.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Připojení vrstvy databázi back-end pro vyrovnávání zatížení

1. Od vaší skupiny prostředků najít nástroje pro vyrovnávání zatížení, který byl vytvořen v předchozích krocích.
2. V části **nastavení**, klikněte na tlačítko **back-endové fondy**a potom klikněte na **přidat** přidat fond back-end.

  ![Nástroj pro vyrovnávání zatížení "Přidat fond back-end."](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Zadejte název pro fond back-end.
4. Přidáte jednotlivé počítače nebo do fondu back-end sadu dostupnosti.

#### <a name="configure-a-probe"></a>Nakonfigurovat test paměti

1. V nástroj pro vyrovnávání zatížení v části **nastavení**, vyberte **sondy**a potom klikněte na **přidat** chcete přidat sondu.

 ![Nástroj pro vyrovnávání zatížení "Přidat test"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Zadejte název pro kontrolu.
3. Vyberte **protokol** kontroly. Pro databázi můžete chtít sondou TCP, nikoli sondu HTTP. Další informace o vyrovnávání zatížení sondy, najdete v tématu [sondy nástroje pro vyrovnávání zatížení Rady pro pochopení](../load-balancer/load-balancer-custom-probe-overview.md).
4. Zadejte **Port** vaší databáze, který se má použít pro přístup k sonda.
5. V části **Interval**, určete, jak často budou do testu aplikace.
6. V části **prahová hodnota špatného stavu**, zadejte počet selhání průběžné testu, které musí být stejné pro virtuální počítač back-end tak, aby byla považována za není v pořádku.
7. Klikněte na tlačítko **OK** vytvořit sonda.

#### <a name="configure-the-load-balancing-rules"></a>Konfigurace pravidel Vyrovnávání zatížení

1. V části **nastavení** nástroj pro vyrovnávání zatížení, vyberte **pravidla Vyrovnávání zatížení**a potom klikněte na **přidat** k vytvoření pravidla.
2. Zadejte **název** pro pravidlo Vyrovnávání zatížení.
3. Vyberte **front-endovou IP adresu** nástroje pro vyrovnávání zatížení, **protokol**, a **Port**.
4. V části **back-endový port**, zadejte port, který se použije ve fondu back-end.
5. Vyberte **fond back-end** a **testu** které byly vytvořené v předchozí postup pravidlo použít.
6. V části **trvalost relace**, vyberte, jak chcete relací se zachovat.
7. V části **časové limity nečinnosti**, zadejte počet minut, než časový limit nečinnosti.
8. V části **plovoucí IP adresa**, vyberte buď **zakázané** nebo **povoleno**.
9. Kliknutím na **OK** vytvořte pravidlo.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Krok 5: Webová vrstva virtuální počítače připojení ke službě Vyrovnávání zatížení

Nyní nakonfigurujeme IP adres a vyrovnávání zatížení front-end port v aplikacích, které jsou spuštěny na webovou vrstvu virtuálních počítačů pro všechna připojení databáze. Tato konfigurace je specifické pro aplikace, které běží na těchto virtuálních počítačích. Konfigurovat cílovou IP adresu a port, naleznete v dokumentaci k aplikaci. IP adresa front-endu, najdete na portálu Azure přejděte do front-endu fond IP adres **nastavení nástroje pro vyrovnávání zatížení**.

![Navigační podokno "IP front-endu fond" nástroje pro vyrovnávání zatížení](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Další postup

* [Přehled služby Traffic Manager](traffic-manager-overview.md)
* [Přehled brány aplikace](../application-gateway/application-gateway-introduction.md)
* [Azure Load Balancer – přehled](../load-balancer/load-balancer-overview.md)
