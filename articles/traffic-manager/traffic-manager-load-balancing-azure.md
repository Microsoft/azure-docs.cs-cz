---
title: Použití služby Vyrovnávání zatížení v Azure | Dokumentace Microsoftu
description: 'V tomto kurzu se dozvíte, jak vytvořit scénář s využitím Azure portfolia Vyrovnávání zatížení: Traffic Manager, služba Application Gateway a nástroje pro vyrovnávání zatížení.'
services: traffic-manager
documentationcenter: ''
author: liumichelle
manager: dkays
editor: ''
ms.assetid: f89be3be-a16f-4d47-bcae-db2ab72ade17
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
ms.openlocfilehash: a6f7a690cac5718216636d3191f348c71bcfb5d6
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734347"
---
# <a name="using-load-balancing-services-in-azure"></a>Použití služby Vyrovnávání zatížení v Azure

## <a name="introduction"></a>Úvod

Microsoft Azure poskytuje několik služeb pro správu, jak se distribuuje síťový provoz s vyrovnáváním zatížení. Můžete použít tyto služby samostatně nebo zkombinovat jejich metod v závislosti na potřebách, abyste mohli sestavit optimální řešení.

V tomto kurzu jsme nejprve definovat případu použití zákazníka a zobrazit jak ji můžete nastavit více robustní a výkonné pomocí následující služby Vyrovnávání zatížení portfolia Azure: Traffic Manager, služba Application Gateway a nástroje pro vyrovnávání zatížení. Pak zajišťuje, že podrobné pokyny pro vytvoření nasazení, které je geograficky redundantní, distribuuje provoz do virtuálních počítačů a pomáhá spravovat různé typy požadavků.

Na koncepční úroveň každou z těchto služeb hraje důležitou roli v hierarchii služby Vyrovnávání zatížení.

* **Traffic Manager** poskytuje Vyrovnávání zatížení globálního DNS. Prohledá příchozí žádosti DNS a odpovídá zprávou funkční koncový bod, v souladu se zásadami směrování vybraného zákazníka. Možnosti pro směrování metody jsou následující:
  * Směrování k odeslání do nejbližší koncového bodu z hlediska latence žadatel výkonu.
  * Priorita směrování směrovat všechen provoz na koncový bod, pomocí dalších koncových bodů jako zálohování.
  * Vážené kruhové dotazování směrování, která distribuuje provoz podle váhy, který je přiřazen do každého koncového bodu.
  * Zeměpisné oblasti založené na směrování za účelem distribuce provozu do koncových bodů vaší aplikace na základě geografického umístění uživatele.
  * Podsíť založené na směrování za účelem distribuce provozu do koncových bodů vaší aplikace na základě podsítě (rozsah IP adres) uživatele.
  * Parametr s více hodnotami, směrování, která umožňují snadno odeslat více aplikačními koncovými body IP adresy v jednu odpověď DNS.

  Klient se připojí přímo na koncový bod vrátil Traffic Managerem. Azure Traffic Manager zjistí, že koncový bod není v pořádku a pak přesměruje klienty k jiné instanci v pořádku. Odkazovat na [dokumentace ke službě Azure Traffic Manager](traffic-manager-overview.md) Další informace o službě.
* **Služba Application Gateway** poskytuje kontroler doručování aplikací (ADC) jako službu, nabízí různé vrstvy 7 možnostmi Vyrovnávání zatížení pro vaši aplikaci. Umožňuje zákazníkům optimalizovat produktivitu webové farmy tím, že se ukončování protokolu SSL náročnou na procesor, ke službě application gateway. Další možnosti přesměrování vrstvy 7 obsahovat kruhové dotazování na distribuci příchozích přenosů, spřažení relace na základě souborů cookie, směrování na základě cesty URL a možnost hostování několika webů za jedné službě application gateway. Application Gateway lze nakonfigurovat jako brány přístupem k Internetu, pouze interní brány nebo kombinaci obojího. Služba Application Gateway je Azure plně spravované, škálovatelné a vysoce dostupné. Nabízí celou řadu možností diagnostiky a protokolování, které zlepšují správu.
* **Nástroj pro vyrovnávání zatížení** je nedílnou součástí Azure SDN stack, poskytuje vysoce výkonné, s nízkou latencí vrstva 4 služby Vyrovnávání zatížení služby pro všechny protokoly UDP a TCP. Spravuje příchozí a odchozí připojení. Můžete nakonfigurovat veřejné a vnitřní s vyrovnáváním zatížení koncových bodů a definovat pravidla pro namapování příchozí připojení do back endového fondu cílů s použitím TCP nebo HTTP zjišťování stavu možností pro správu dostupnost služeb.

## <a name="scenario"></a>Scénář

V tomto ukázkovém scénáři doporučujeme použít jednoduchý web, který poskytuje dva typy obsahu: imagí a dynamicky vykreslené webové stránky. Webu musí být geograficky redundantní a jeho uživatelům nejblíž (nejnižší latence) by měl sloužit umístění k nim. Vývojář aplikace se rozhodla, že žádné adresy URL, které odpovídají vzoru/imagí / * se obsluhují z vyhrazený fond virtuálních počítačů, které se liší od zbývající části webové farmy.

Výchozí fond virtuální počítač obsluhující dynamického obsahu se navíc musí komunikovat s back-end databáze, která je hostovaná v clusteru s vysokou dostupností. Celé nasazení je nastavený prostřednictvím Azure Resource Manageru.

Pomocí služby Traffic Manager, služba Application Gateway a nástroje pro vyrovnávání zatížení umožňuje tento web k dosažení těchto cílů návrhu:

* **Redundance geografickým oblastem**: Pokud jedna oblast přestane fungovat, Traffic Manager směruje provoz bez problémů do nejbližší oblasti bez jakéhokoli zásahu od vlastníka aplikace.
* **Nižší latenci**: Vzhledem k tomu, že Traffic Manager automatickému směrování zákazníků do nejbližší oblasti, zákazník narazí nižší latenci a pokud se požaduje obsah webové stránky.
* **Nezávislého škálování**: Protože zatížení webové aplikace jsou oddělené oddělovačem typ obsahu, vlastník aplikace může škálovat nezávisle na sobě navzájem požadavku úlohy. Služba Application Gateway zajišťuje, že provoz se směruje do správné fondů na základě určených pravidel a stav aplikace.
* **Interní Vyrovnávání zatížení**: Protože nástroj pro vyrovnávání zatížení před tímto clusterem vysokou dostupnost, je přístupný jenom aktivní a funkční koncový bod pro databázi aplikace. Kromě toho správce databáze, můžete optimalizovat zatížení díky distribuci aktivní a pasivní replik napříč clusterem nezávisle na front-endové aplikace. Nástroj pro vyrovnávání zatížení poskytuje připojení ke clusteru vysokou dostupnost a zajistí, že pouze v dobrém stavu databáze přijímat žádosti o připojení.

Následující diagram znázorňuje architekturu tento scénář:

![Diagram služby Vyrovnávání zatížení architektury](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> V tomto příkladu je pouze jeden z mnoha možné konfigurace služby Vyrovnávání zatížení, které nabízí Azure. Traffic Manager, služba Application Gateway a služby Load balancer úrovně lze kombinovat a spárují s nejlepší vyhovovala vašim potřebám Vyrovnávání zatížení. Například pokud přesměrování zpracování SSL nebo vrstvy 7 zpracování není nezbytné, nástroj pro vyrovnávání zatížení může být zastoupen Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>Nastavení služby Vyrovnávání zatížení zásobníku

### <a name="step-1-create-a-traffic-manager-profile"></a>Krok 1: Vytvoření profilu Traffic Manageru

1. Na webu Azure Portal, klikněte na tlačítko **vytvořit prostředek** > **sítě** > **profil služby Traffic Manager**  >   **Vytvoření**.
2. Zadejte následující informace:

  * **Název**: Zadejte svůj profil Traffic Manageru DNS název předpony.
  * **Metody směrování**: Vyberte zásadu metodu směrování provozu. Další informace o metodách, naleznete v tématu [metody směrování provozu Traffic Manageru](traffic-manager-routing-methods.md).
  * **Předplatné**: Vyberte předplatné, obsahuje profil.
  * **Skupina prostředků**: Vyberte skupinu prostředků, který obsahuje profil. Může být nové nebo existující skupinu prostředků.
  * **Umístění skupiny prostředků**: Služba Traffic Manager je globální a není vázaná na místo. Musíte však zadat oblast pro skupinu, ve kterém se budou metadata spojená s profilem Traffic Manager nachází. Toto umístění nemá žádný vliv na běhovou dostupnost profilu.

3. Klikněte na tlačítko **vytvořit** k vygenerování profilu služby Traffic Manager.

  !["Vytvořit Traffic Manageru" okno](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Krok 2: Vytvoření brány application Gateway

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **vytvořit prostředek** > **sítě** > **Application Gateway**.
2. Zadejte následující informace o službě application gateway:

  * **Název**: Název služby application gateway.
  * **Velikost SKU**: Velikost application gateway, k dispozici jako malé, střední nebo velké.
  * **Počet instancí**: Počet instancí, hodnota od 2 do 10.
  * **Skupina prostředků**: Skupina prostředků, která obsahuje službu application gateway. Může být existující skupinu prostředků nebo nové.
  * **Umístění**: Oblast pro službu application gateway, která je na stejném umístění jako skupina prostředků. Umístění je důležité, protože virtuální sítě a veřejné IP adresy musí být ve stejném umístění jako brána.
3. Klikněte na **OK**.
4. Definujte virtuální sítě, podsítě, front-end IP adresu a konfigurace naslouchacího procesu pro službu application gateway. V tomto scénáři je front-endovou IP adresu **veřejné**, což umožňuje přidat jako koncový bod do profilu služby Traffic Manager později.
5. Nakonfigurujte naslouchací proces s jedním z následujících možností:
    * Pokud používáte protokol HTTP, není nutné nic konfigurovat. Klikněte na **OK**.
    * Pokud používáte protokol HTTPS, další konfigurace je nutná. Odkazovat na [vytvoření služby application gateway](../application-gateway/application-gateway-create-gateway-portal.md)začíná v kroku 9. Po dokončení konfigurace klikněte na tlačítko **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurace směrování adres URL pro brány application Gateway

Při výběru back endového fondu trvá aplikační brány, který je nakonfigurovaný s pravidlem na základě cest vzor cesty adresy URL žádosti kromě kruhové dotazování na distribuci. V tomto scénáři přidáváme pravidlo na základě cest pro přesměrování libovolnou adresu URL s "/images/\*" do fondu serverů, bitové kopie. Další informace o konfiguraci adresy URL na základě cest pro aplikační bránu, směrování najdete [vytvořit pravidlo na základě cest pro aplikační bránu](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagram vrstvy webové aplikace brány](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Vaší skupiny prostředků přejděte do instance služby application gateway, kterou jste vytvořili v předchozí části.
2. V části **nastavení**vyberte **back-endové fondy**a pak vyberte **přidat** přidáte virtuální počítače, které chcete přidružit k fondy back endové webové vrstvy.
3. Zadejte název back endového fondu a všechny IP adresy počítačů, které se nacházejí ve fondu. V tomto scénáři se připojujete dva fondy back endového serveru, virtuálních počítačů.

  ![Služba Application Gateway "Přidat back-endový fond"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. V části **nastavení** služby application gateway, vyberte **pravidla**a potom klikněte na tlačítko **na základě cest** tlačítko pro přidání pravidlo.

  ![Tlačítko "Na základě cest" pravidla bránu aplikace](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Tím, že poskytuje následující informace o konfiguraci pravidla.

   Základní nastavení:

   + **Název**: Popisný název pravidla, které je dostupné na portálu.
   + **Naslouchací proces**: Naslouchací proces, který se používá pro pravidlo.
   + **Výchozí back-endový fond**: Back endového fondu, který se má použít výchozí pravidlo.
   + **Výchozí nastavení protokolu HTTP**: Nastavení HTTP pro použití s výchozí pravidlo.

   Pravidla na základě cest:

   + **Název**: Popisný název pravidla na základě cest.
   + **Cesty**: Pravidlo cesty, který se používá pro přesměrování provozu.
   + **Back-endový fond**: Back endového fondu, který se má použít s tímto pravidlem.
   + **Nastavení HTTP**: Nastavení HTTP, který se má použít s tímto pravidlem.

   > [!IMPORTANT]
   > Cesty: Platná cesta musí začínat znakem "/". Zástupný znak "\*" je povolen pouze na konci. Příklady platných jsou /xyz /xyz\*, nebo /xyz/\*.

   ![Okno "Přidat pravidlo na základě cest" brány aplikací](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Krok 3: Přidání brány application Gateway pro koncové body Traffic Manageru

V tomto scénáři je Traffic Manager připojené k branám application Gateway (podle konfigurace v předchozích krocích), které se nacházejí v různých oblastech. Teď, když jsou nakonfigurované brány application Gateway, dalším krokem je pro připojení k vašemu profilu Traffic Manageru.

1. Otevřete svůj profil Traffic Manageru. Uděláte to tak, podívejte se ve vaší skupině prostředků nebo vyhledejte název profilu Traffic Manageru z **všechny prostředky**.
2. V levém podokně vyberte **koncové body**a potom klikněte na tlačítko **přidat** k přidání koncového bodu.

  ![Traffic Manager koncové body "Přidat" tlačítko](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Vytvořte koncový bod tak, že zadáte následující informace:

  * **Typ**: Vyberte typ koncový bod Vyrovnávání zatížení. V tomto scénáři vyberte **koncový bod Azure** vzhledem k tomu, že se připojujete k instancím brány aplikací, které byly dříve nakonfigurovány.
  * **Název**: Zadejte název koncového bodu.
  * **Typ cílového prostředku**: Vyberte **veřejnou IP adresu** a pak v části **cílový prostředek**, vyberte veřejné IP adresy služby application gateway, která byla dříve nakonfigurovaná.

   ![Traffic Manager "Přidat koncový bod"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Teď můžete otestovat nastavení přistupování k aplikacím s DNS vašeho profilu Traffic Manageru (v tomto příkladu: TrafficManagerScenario.trafficmanager.net). Můžete znovu odeslat požadavky, vyvolali nebo přenést virtuální počítače a webové servery, které byly vytvořeny v různých oblastech a změnit nastavení profilu Traffic Manageru k otestování nastavení.

### <a name="step-4-create-a-load-balancer"></a>Krok 4: Vytvoření nástroje pro vyrovnávání zatížení

V tomto scénáři nástroje pro vyrovnávání zatížení distribuuje připojení z webové vrstvy do databází v rámci clusteru s vysokou dostupností.

Pokud váš cluster vysokou dostupnost databáze používá SQL Server AlwaysOn, podívejte se na [nakonfigurovat jeden nebo více vždy na naslouchacích procesů skupin dostupnosti](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) podrobné pokyny.

Další informace o konfiguraci interního nástroje najdete v tématu [vytvoření interního nástroje na webu Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **vytvořit prostředek** > **sítě** > **nástroje pro vyrovnávání zatížení**.
2. Zvolte název pro nástroj pro vyrovnávání zatížení.
3. Nastavte **typ** k **interní**a vyberte příslušnou virtuální síť a podsíť pro nástroj pro vyrovnávání zatížení se nacházejí v.
4. V části **přiřazení IP adresy**, vyberte buď **dynamické** nebo **statické**.
5. V části **skupiny prostředků**, vyberte skupinu prostředků, nástroje pro vyrovnávání zatížení.
6. V části **umístění**, vyberte příslušnou oblast, nástroj pro vyrovnávání zatížení.
7. Klikněte na tlačítko **vytvořit** ke generování nástroje pro vyrovnávání zatížení.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Připojit back-end databázová vrstva nástroji pro vyrovnávání zatížení

1. Z vaší skupiny prostředků vyhledejte nástroj pro vyrovnávání zatížení, který byl vytvořen v předchozích krocích.
2. V části **nastavení**, klikněte na tlačítko **back-endové fondy**a potom klikněte na tlačítko **přidat** přidat back endového fondu.

  ![Nástroj pro vyrovnávání zatížení "Přidat back-endový fond"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Zadejte název back endového fondu.
4. Přidejte jednotlivé počítače nebo dostupnosti do back endového fondu.

#### <a name="configure-a-probe"></a>Konfigurace testu

1. Ve službě load balancer v části **nastavení**vyberte **sondy**a potom klikněte na tlačítko **přidat** přidat sondu.

 ![Nástroj pro vyrovnávání zatížení "Přidat test"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Zadejte název pro test paměti.
3. Vyberte **protokol** pro test paměti. Pro databázi může být vhodné sondu protokolu TCP spíše než sondu protokolu HTTP. Chcete-li další informace o sondy nástroje pro vyrovnávání zatížení, přečtěte si [porozumění testům nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md).
4. Zadejte **Port** databáze se použije pro přístup k testu.
5. V části **Interval**, určete, jak často aplikace testovat.
6. V části **prahová hodnota špatného stavu**, zadejte počet selhání průběžné testu, které se musí vyskytovat virtuálního počítače back-end se považoval za poškozený.
7. Klikněte na tlačítko **OK** vytvořte test.

#### <a name="configure-the-load-balancing-rules"></a>Konfigurace pravidel Vyrovnávání zatížení

1. V části **nastavení** vašeho nástroje pro vyrovnávání zatížení vyberte **pravidla Vyrovnávání zatížení**a potom klikněte na tlačítko **přidat** a vytvořte pravidlo.
2. Zadejte **název** pravidla Vyrovnávání zatížení.
3. Zvolte **front-endové IP adresy** nástroje pro vyrovnávání zatížení **protokol**, a **Port**.
4. V části **back-endový port**, zadejte port, který se má použít v back endového fondu.
5. Vyberte **back-endový fond** a **Probe** , které byly vytvořeny v předchozích krocích pro pravidlo použít.
6. V části **trvalost relace**, vyberte, jak chcete uchovávat relace.
7. V části **vypršení časového limitu nečinnosti**, zadejte počet minut, než časový limit nečinnosti.
8. V části **plovoucí IP adresy**, vyberte buď **zakázané** nebo **povoleno**.
9. Kliknutím na **OK** vytvořte pravidlo.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Krok 5: Připojte virtuální počítače webové vrstvy do nástroje pro vyrovnávání zatížení

Nyní nakonfigurujeme IP adres a nástroje pro vyrovnávání zatížení front-endový port v aplikacích, které jsou spuštěny na vaše virtuální počítače webové vrstvy pro všechna připojení databáze. Tato konfigurace je specifické pro aplikace, které běží na těchto virtuálních počítačů. Pokud chcete nakonfigurovat, cílová IP adresa a port, najdete v dokumentaci aplikace. Pokud chcete zjistit IP adresu front-endu na webu Azure Portal, přejděte na front-endový fond IP adres **nastavení nástroje pro vyrovnávání zatížení**.

![Navigační podokno "IP front-endový fond" nástroje pro vyrovnávání zatížení](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Další postup

* [Přehled služby Traffic Manager](traffic-manager-overview.md)
* [Přehled služby Application Gateway](../application-gateway/application-gateway-introduction.md)
* [Azure Load Balancer – přehled](../load-balancer/load-balancer-overview.md)
