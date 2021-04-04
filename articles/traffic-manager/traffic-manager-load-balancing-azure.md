---
title: Používání služeb vyrovnávání zatížení v Azure | Microsoft Docs
description: 'V tomto kurzu se dozvíte, jak vytvořit scénář pomocí portfolia vyrovnávání zatížení Azure: Traffic Manager, Application Gateway a Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: duau
ms.openlocfilehash: eaf50f3bdacaf5680bc5ecb1379faff20133b5ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184369"
---
# <a name="using-load-balancing-services-in-azure"></a>Použití služeb pro vyrovnávání zatížení v Azure

## <a name="introduction"></a>Úvod

Microsoft Azure poskytuje několik služeb pro správu způsobu distribuce a vyrovnávání zatížení v provozu v síti. Tyto služby můžete použít samostatně nebo zkombinovat své metody podle vašich potřeb, abyste vytvořili optimální řešení.

V tomto kurzu nejdřív definujeme případ použití zákazníka a zjistíte, jak se dá provádět robustnější, a to pomocí následujícího portfolia pro vyrovnávání zatížení Azure: Traffic Manager, Application Gateway a Load Balancer. Pak poskytujeme podrobné pokyny pro vytvoření geograficky redundantního nasazení, distribuci provozu do virtuálních počítačů a pomůže vám spravovat různé typy požadavků.

Na koncepční úrovni každá z těchto služeb hraje jedinečnou roli v hierarchii vyrovnávání zatížení.

* **Traffic Manager** poskytuje globální vyrovnávání zatížení DNS. Vyhledává příchozí požadavky DNS a reaguje s dobrým koncovým bodem v souladu se zásadami směrování, které zákazník vybral. Pro metody směrování jsou k disdobu tyto možnosti:
  * Směrování výkonu pro odeslání žadatele do nejbližšího koncového bodu z podmínek latence.
  * Priorita směrování, která směruje veškerý provoz do koncového bodu s ostatními koncovými body jako záložní.
  * Vážené směrování s kruhovým dotazováním, které distribuuje provoz na základě váhy přiřazené ke každému koncovému bodu.
  * Směrování založené na geografických oblastech pro distribuci provozu do koncových bodů aplikace na základě geografického umístění uživatele.
  * Směrování založené na podsíti pro distribuci provozu do koncových bodů aplikace na základě podsítě (rozsahu IP adres) uživatele.
  * Směrování s více hodnotami, které umožňuje odesílat IP adresy více než jednoho koncového bodu aplikace v rámci jedné odpovědi DNS.

  Klient se připojuje přímo ke koncovému bodu vrácenému Traffic Manager. Azure Traffic Manager detekuje, kdy koncový bod není v pořádku, a pak přesměruje klienty na jinou funkční instanci. Další informace o této službě najdete v [dokumentaci k Azure Traffic Manager](traffic-manager-overview.md) .
* **Application Gateway** poskytuje aplikaci Service Delivery Controller (ADC) jako službu a nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaši aplikaci. Umožňuje zákazníkům optimalizovat produktivitu webové farmy tím, že v aplikační bráně předává ukončení protokolu TLS náročné na procesor. Mezi další možnosti směrování vrstvy 7 patří kruhové dotazování příchozích přenosů, spřažení relací na základě souborů cookie, směrování na základě cesty URL a možnost hostování několika webů za jedinou aplikační bránou. Application Gateway lze nakonfigurovat jako bránu internetovou bránu, pouze pro interní bránu nebo kombinaci obou. Application Gateway je plně spravovaná, škálovatelná a vysoká dostupnost Azure. Nabízí celou řadu možností diagnostiky a protokolování, které zlepšují správu.
* **Load Balancer** je nedílnou součástí zásobníku Azure SDN, která poskytuje vysoce výkonné služby Vyrovnávání zatížení vrstvy 4 s nízkou latencí pro všechny protokoly UDP a TCP. Spravuje příchozí a odchozí připojení. Pokud chcete spravovat dostupnost služby, můžete nakonfigurovat veřejné a interní koncové body s vyrovnáváním zatížení a definovat pravidla pro mapování příchozích připojení na cíle v backendovém fondu s využitím možností monitorování stavu protokolů TCP a HTTP.

## <a name="scenario"></a>Scenario

V tomto ukázkovém scénáři používáme jednoduchý web, který slouží jako dva typy obsahu: obrazy a dynamicky vykreslené webové stránky. Web musí být geograficky redundantní a měl by sloužit svým uživatelům z nejbližšího umístění (nejnižší latence). Vývojář aplikace rozhodl, že všechny adresy URL, které odpovídají vzoru/images/*, se zpracovávají z vyhrazeného fondu virtuálních počítačů, které se liší od zbytku webové farmy.

Kromě toho musí výchozí fond virtuálních počítačů, který obsluhuje dynamický obsah, komunikovat s back-end databází, která je hostována v clusteru s vysokou dostupností. Celé nasazení je nastaveno prostřednictvím Azure Resource Manager.

Použití Traffic Manager, Application Gateway a Load Balancer umožňuje tomuto webu dosáhnout těchto cílů návrhu:

* **Geografická redundance**: Pokud dojde k výpadku jedné oblasti, Traffic Manager směrovat provoz plynule do nejbližší oblasti bez zásahu vlastníka aplikace.
* **Omezená latence**: protože Traffic Manager automaticky přesměruje zákazníka do nejbližší oblasti, činnost zákazníka při vyžádání obsahu webové stránky sníží latenci.
* **Nezávislá škálovatelnost**: vzhledem k tomu, že zatížení webové aplikace je oddělené podle typu obsahu, může vlastník aplikace škálovat zatížení požadavků nezávisle na sobě. Application Gateway zajišťuje směrování provozu do správných fondů na základě zadaných pravidel a stavu aplikace.
* **Interní vyrovnávání zatížení**: vzhledem k tomu, že Load Balancer je před clusterem s vysokou dostupností, je aplikaci zpřístupněn pouze aktivní a dobrý koncový bod pro databázi. Kromě toho může správce databáze optimalizovat zatížení distribucí aktivních a pasivních replik v clusteru nezávisle na front-endové aplikaci. Load Balancer doručuje připojení do clusteru s vysokou dostupností a zajišťuje, aby požadavky na připojení přijímaly jenom databáze v pořádku.

Následující diagram znázorňuje architekturu tohoto scénáře:

![Diagram architektury vyrovnávání zatížení](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> V tomto příkladu je jenom jedna z mnoha možných konfigurací služeb vyrovnávání zatížení, které Azure nabízí. Traffic Manager, Application Gateway a Load Balancer je možné kombinovat a odpovídat nejlépe vašim potřebám vyrovnávání zatížení. Například pokud není nutné zpracování TLS nebo zpracování vrstvy 7, Load Balancer lze použít místo Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>Nastavení zásobníku vyrovnávání zatížení

### <a name="step-1-create-a-traffic-manager-profile"></a>Krok 1: vytvoření profilu Traffic Manager

1. V Azure Portal klikněte na **vytvořit prostředek**  >  **síť**  >  **Traffic Manager profil**  >  **vytvořit**.
2. Zadejte následující základní informace:

   * **Název**: zadejte profil Traffic Manager název předpony DNS.
   * **Metoda směrování**: vyberte zásadu metody směrování provozu. Další informace o metodách najdete v tématu [informace o metodách směrování provozu služby Traffic Manager](traffic-manager-routing-methods.md).
   * **Předplatné**: vyberte předplatné, které obsahuje profil.
   * **Skupina prostředků**: vyberte skupinu prostředků, která obsahuje profil. Může se jednat o novou nebo existující skupinu prostředků.
   * **Umístění skupiny prostředků**: služba Traffic Manager je globální a není vázaná na umístění. Je však nutné zadat oblast pro skupinu, kde jsou uložena Metadata přidružená k profilu Traffic Manager. Toto umístění nemá žádný vliv na dostupnost tohoto profilu za běhu.

3. Kliknutím na **vytvořit** vygenerujte profil Traffic Manager.

   ![Okno pro vytvoření Traffic Manager](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Krok 2: vytvoření aplikačních bran

1. V Azure Portal v levém podokně klikněte na **vytvořit prostředek**  >  **sítě**  >  **Application Gateway**.
2. Zadejte následující základní informace o aplikační bráně:

   * **Název**: název aplikační brány.
   * **Velikost SKU**: velikost aplikační brány, která je dostupná jako malá, střední nebo velká.
   * **Počet** instancí: počet instancí, hodnota od 2 do 10.
   * **Skupina prostředků**: Skupina prostředků, která obsahuje aplikační bránu. Může se jednat o existující skupinu prostředků nebo o novou.
   * **Location (umístění**): oblast pro aplikační bránu, která je stejně umístění jako skupina prostředků. Umístění je důležité, protože virtuální síť a veřejná IP adresa musí být ve stejném umístění jako brána.
3. Klikněte na **OK**.
4. Definujte konfigurace virtuální sítě, podsítě, front-endové IP adresy a naslouchacího procesu pro službu Application Gateway. V tomto scénáři je front-end IP adresa **Veřejná**, což umožňuje později přidat jako koncový bod do profilu Traffic Manager.
5. Nakonfigurujte naslouchací proces pomocí jedné z následujících možností:
    * Pokud používáte protokol HTTP, není k dispozici žádná konfigurace. Klikněte na **OK**.
    * Pokud používáte protokol HTTPS, je vyžadována další konfigurace. Podívejte se na téma [Vytvoření aplikační brány](../application-gateway/quick-create-portal.md), počínaje krokem 9. Po dokončení konfigurace klikněte na **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurace směrování adres URL pro brány Application Gateway

Když zvolíte fond back-end, aplikace služby Application Gateway nakonfigurovaná s pravidlem na základě cesty má kromě distribuce kruhové dotazování také vzor cesty adresy URL požadavku. V tomto scénáři přidáváme pravidlo na základě cesty, které bude směrovat každou adresu URL s názvem "/images/ \* " do fondu imagí serveru. Další informace o konfiguraci směrování na základě cesty URL pro službu Application Gateway najdete v tématu [Vytvoření pravidla založeného na cestách pro službu Application Gateway](../application-gateway/create-url-route-portal.md).

![Application Gateway diagram webové vrstvy](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Ve vaší skupině prostředků přejdete do instance služby Application Gateway, kterou jste vytvořili v předchozí části.
2. V části **Nastavení** vyberte **back-endové fondy** a pak vyberte **Přidat** a přidejte tak virtuální počítače, které chcete přidružit k fondům back-endu na webové úrovni.
3. Zadejte název back-end fondu a všechny IP adresy počítačů, které jsou ve fondu. V tomto scénáři propojujeme dva fondy back-end serverů virtuálních počítačů.

   ![Application Gateway "Přidat fond back-endu"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. V části **Nastavení** aplikační brány vyberte **pravidla** a potom kliknutím na tlačítko na **základě cesty** přidejte pravidlo.

   ![Pravidla Application Gateway – tlačítko založené na cestě](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Nakonfigurujte pravidlo zadáním následujících informací.

   Základní nastavení:

   + **Název**: popisný název pravidla, které je přístupné na portálu.
   + **Naslouchací proces**: naslouchací proces, který se používá pro pravidlo.
   + **Výchozí back**-end fond: fond back-end, který se má použít s výchozím pravidlem.
   + **Výchozí nastavení http**: nastavení HTTP, které se má použít s výchozím pravidlem.

   Pravidla na základě cest:

   + **Název**: popisný název pravidla na základě cesty.
   + **Cesty**: pravidlo cesty, které se používá pro přesměrování provozu.
   + **Back**-end fond: fond back-endu, který se má používat s tímto pravidlem.
   + **Nastavení http**: nastavení protokolu HTTP, které se má použít s tímto pravidlem.

   > [!IMPORTANT]
   > Cesty: platné cesty musí začínat znakem "/". Zástupný znak " \* " je povolen pouze na konci. Platnými příklady jsou/XYZ,/XYZ \* nebo/XYZ/ \* .

   ![Application Gateway okno Přidat pravidlo na základě cesty](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Krok 3: Přidání aplikačních bran do koncových bodů Traffic Manager

V tomto scénáři je Traffic Manager připojený k aplikačním branám (jak je nakonfigurované v předchozích krocích), které se nacházejí v různých oblastech. Teď, když jsou brány Application Gateway nakonfigurované, je dalším krokem připojení k profilu Traffic Manager.

1. Otevřete profil Traffic Manager. Provedete to tak, že ve své skupině prostředků vyhledáte název profilu Traffic Manager ze **všech prostředků**.
2. V levém podokně vyberte **koncové body** a potom kliknutím na **Přidat** přidejte koncový bod.

   ![Tlačítko pro přidání koncových bodů Traffic Manager](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Vytvořte koncový bod zadáním následujících informací:

   * **Typ**: Vyberte typ koncového bodu pro vyrovnávání zatížení. V tomto scénáři vyberte **koncový bod Azure** , protože ho připojujeme k instancím služby Application Gateway, které jste nakonfigurovali dříve.
   * **Název**: zadejte název koncového bodu.
   * **Typ cílového prostředku**: vyberte **Veřejná IP adresa** a potom v části **cílový prostředek** vyberte veřejnou IP adresu služby Application Gateway, která byla nakonfigurovaná dřív.

   ![Traffic Manager "přidat koncový bod"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Nyní můžete otestovat nastavení tím, že k němu přistupujete pomocí DNS profilu Traffic Manager (v tomto příkladu: TrafficManagerScenario.trafficmanager.net). Můžete znovu odeslat požadavky, vyvolat nebo vyřadit virtuální počítače a webové servery, které byly vytvořeny v různých oblastech, a změnit nastavení profilu Traffic Manager pro otestování instalace.

### <a name="step-4-create-a-load-balancer"></a>Krok 4: Vytvoření nástroje pro vyrovnávání zatížení

V tomto scénáři Load Balancer distribuuje připojení z webové vrstvy k databázím v clusteru s vysokou dostupností.

Pokud databázový cluster s vysokou dostupností používá SQL Server AlwaysOn, přečtěte si téma [konfigurace jednoho nebo více naslouchacího procesu skupiny dostupnosti Always On](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) pro podrobné pokyny.

Další informace o konfiguraci interního nástroje pro vyrovnávání zatížení najdete [v tématu Vytvoření interního nástroje pro vyrovnávání zatížení v Azure Portal](../load-balancer/quickstart-load-balancer-standard-internal-portal.md).

1. V Azure Portal v levém podokně klikněte na **vytvořit prostředek**  >  **síťový**  >  **Nástroj pro vyrovnávání zatížení**.
2. Vyberte název svého nástroje pro vyrovnávání zatížení.
3. Nastavte **typ** na **interní** a vyberte odpovídající virtuální síť a podsíť, ve které se má nástroj pro vyrovnávání zatížení nacházet.
4. V části **přiřazení IP adresy** vyberte možnost **Dynamická** nebo **statická**.
5. V části **Skupina prostředků** vyberte skupinu prostředků pro nástroj pro vyrovnávání zatížení.
6. V části **umístění** vyberte příslušnou oblast pro nástroj pro vyrovnávání zatížení.
7. Kliknutím na **vytvořit** vygenerujte Nástroj pro vyrovnávání zatížení.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Připojení databázové vrstvy back-end k nástroji pro vyrovnávání zatížení

1. Z vaší skupiny prostředků Najděte Nástroj pro vyrovnávání zatížení, který jste vytvořili v předchozích krocích.
2. V části **Nastavení** klikněte na **back-endové fondy** a potom kliknutím na **Přidat** přidejte fond back-end.

   ![Load Balancer "Přidat fond back-endu"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Zadejte název back-endu fondu.
4. Přidejte do fondu back-end buď jednotlivé počítače, nebo skupinu dostupnosti.

#### <a name="configure-a-probe"></a>Konfigurace testu paměti

1. V nástroji pro vyrovnávání zatížení v části **Nastavení** vyberte **sondy** a pak kliknutím na **Přidat** přidejte test.

   ![Load Balancer "Přidání testu paměti"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Zadejte název testu.
3. Vyberte **protokol** pro test. V případě databáze můžete chtít test TCP místo testu HTTP. Další informace o sondách pro vyrovnávání zatížení najdete v tématu [Principy sond nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md).
4. Zadejte **port** vaší databáze, který se má použít pro přístup k testu.
5. V části **interval** zadejte, jak často se má aplikace testovat.
6. V části **stavová prahová hodnota** zadejte počet nepřetržitých selhání testu, které se musí vyskytnout, aby se virtuální počítač back-end považoval za špatný.
7. Kliknutím na **OK** vytvořte test.

#### <a name="configure-the-load-balancing-rules"></a>Konfigurace pravidel vyrovnávání zatížení

1. V části **Nastavení** nástroje pro vyrovnávání zatížení vyberte **pravidla vyrovnávání zatížení** a potom kliknutím na **Přidat** vytvořte pravidlo.
2. Zadejte **název** pravidla vyrovnávání zatížení.
3. Vyberte **IP adresu front-endu** , **protokolu** a **portu** pro vyrovnávání zatížení.
4. V části **port back-endu** zadejte port, který se má použít ve fondu back-end.
5. Vyberte **back-end fond** a **test** , který jste vytvořili v předchozích krocích, aby se pravidlo používalo.
6. V části **trvalá relace** vyberte, jak chcete, aby relace trvaly.
7. V části **časový limit nečinnosti** zadejte počet minut před vypršením časového limitu nečinnosti.
8. V části **plovoucí IP adresa** vyberte možnost **zakázáno** nebo **povoleno**.
9. Kliknutím na **OK** vytvořte pravidlo.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Krok 5: připojení virtuálních počítačů webové vrstvy k nástroji pro vyrovnávání zatížení

Teď nakonfigurujeme front-end port IP a modul pro vyrovnávání zatížení v aplikacích, které běží na virtuálních počítačích na webové úrovni pro všechna databázová připojení. Tato konfigurace je specifická pro aplikace, které běží na těchto virtuálních počítačích. Chcete-li nakonfigurovat cílovou IP adresu a port, přečtěte si dokumentaci k aplikaci. Pokud chcete najít IP adresu front-endu, v Azure Portal v **nastavení nástroje pro vyrovnávání zatížení** přejít do fondu front-end IP adres.

![Load Balancer navigačním podokně "fond IP adres front-endu"](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Další kroky

* [Přehled služby Traffic Manager](traffic-manager-overview.md)
* [Přehled služby Application Gateway](../application-gateway/overview.md)
* [Azure Load Balancer – přehled](../load-balancer/load-balancer-overview.md)