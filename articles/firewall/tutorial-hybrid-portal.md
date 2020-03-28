---
title: 'Kurz: Nasazení a konfigurace Azure Firewall v hybridní síti pomocí portálu Azure'
description: V tomto kurzu se dozvíte, jak nasadit a nakonfigurovat Azure Firewall pomocí portálu Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 208a7a677bdf0b76ffed83e679c6f1ff3041d50d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239687"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Kurz: Nasazení a konfigurace Azure Firewall v hybridní síti pomocí portálu Azure

Když připojíte místní síť k virtuální síti Azure a vytvoříte hybridní síť, je možnost řídit přístup k prostředkům sítě Azure důležitou součástí celkového plánu zabezpečení.

Pomocí brány Azure Firewall můžete řídit přístup k síti v hybridní síti pomocí pravidel, která definují povolený a odepřený síťový provoz.

Pro účely tohoto kurzu vytvoříte tři virtuální sítě:

- **VNet-Hub** - brána firewall je v této virtuální síti.
- **Virtuální síť-Spoke** – virtuální síť pro paprsky představuje úlohu umístěnou v Azure.
- **VNet-Onprem** – místní virtuální síť představuje místní síť. Ve skutečném nasazení může být připojen pomocí připojení VPN nebo ExpressRoute. Pro jednoduchost tento kurz používá připojení brány VPN a virtuální síť umístěná v Azure se používá k reprezentaci místní sítě.

![Brána firewall v hybridní síti](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Deklarování proměnných
> * Vytvoření virtuální sítě centra brány firewall
> * Vytvoření virtuální sítě paprsku
> * Vytvoření místní virtuální sítě
> * Konfigurace a nasazení brány firewall
> * Vytvoření a propojení bran VPN
> * Vzájemné připojení virtuálních sítí rozbočovače a paprsků
> * Vytvoření tras
> * Vytvoření virtuálních počítačů
> * Testování brány firewall

Pokud k dokončení tohoto postupu chcete místo toho použít Azure PowerShell, přečtěte si téma [Nasazení a konfigurace Azure Firewall v hybridní síti pomocí Azure PowerShellu](tutorial-hybrid-ps.md).

## <a name="prerequisites"></a>Požadavky

Hybridní síť používá model architektury rozbočovače a paprsku ke směrování provozu mezi virtuálními sítěmi Azure a místními sítěmi. Architektura hub-and-spoke má následující požadavky:

- Nastavte **AllowGatewayTransit** při partnerského vztahu virtuální sítě-Hub na Virtuální síť-Spoke. V síťové architektuře hub-and-spoke umožňuje přenos brány virtuálním sítím s paprsky sdílet bránu VPN v rozbočovači namísto nasazování bran VPN v každé virtuální síti s paprsky. 

   Kromě toho se trasy do virtuálních sítí připojených k bráně nebo místních sítí automaticky rozšíří do směrovacích tabulek pro virtuální sítě partnerského vztahu pomocí přenosu brány. Další informace naleznete v [tématu Konfigurace přenosu brány VPN pro partnerský vztah virtuální sítě](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Nastavte **useRemoteGateways,** když jste peer Virtuální síť-mluvil do Virtuální sítě-Hub. Pokud je nastavena **možnost UseRemoteGateways** a **je nastavena také funkce AllowGatewayTransit** ve vzdáleném partnerské síti, virtuální síť pro paprsky používá brány vzdálené virtuální sítě pro přenos.
- Chcete-li směrovat provoz paprskové podsítě přes bránu firewall centra, potřebujete uživatelsky definovanou trasu (UDR), která odkazuje na bránu firewall s zakázanou možností **šíření trasy brány virtuální sítě.** Možnost **šíření směrování brány virtuální sítě** zabraňuje distribuci trasy do podsítí paprsků. Tím zabráníte tomu, aby se učené trasy dostaly do konfliktu s udr.
- Nakonfigurujte UDR v podsíti brány rozbočovače, která odkazuje na ip adresu brány firewall jako další směrování do sítí paprskových. V podsíti Azure Firewall není vyžadováno žádné UDR, protože se učí trasy z protokolu BGP.

Postup vytvoření těchto tras najdete v části [Vytvoření pravidel](#create-the-routes) v tomto kurzu.

>[!NOTE]
>Azure Firewall musí mít přímé připojení k Internetu. Pokud se vaše síť AzureFirewallSubnet učí výchozí trasu do místní sítě prostřednictvím protokolu BGP, musíte to přepsat udr 0.0.0.0/0 s hodnotou **NextHopType** nastavenou jako **Internet,** aby bylo zachováno přímé připojení k Internetu.
>
>Azure Firewall lze nakonfigurovat tak, aby podporovala vynucené tunelové propojení. Další informace naleznete v tématu [Vynucené tunelové propojení azure brány Azure Firewall](forced-tunneling.md).

>[!NOTE]
>Provoz mezi přímo partnerskými virtuálními sítěmi se směruje přímo, i když UDR odkazuje na Azure Firewall jako výchozí bránu. Chcete-li odeslat podsíť do provozu podsítě do brány firewall v tomto scénáři, musí udr obsahovat předponu sítě cílové podsítě explicitně v obou podsítích.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-the-firewall-hub-virtual-network"></a>Vytvoření virtuální sítě centra brány firewall

Nejprve vytvořte skupinu prostředků, která bude obsahovat prostředky pro tento kurz:

1. Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .
2. Na domovské stránce portálu Azure vyberte **Skupiny** > prostředků**Přidat**.
3. Do **pole Název skupiny prostředků**zadejte příkaz **FW-Hybrid-Test**.
4. V části **Předplatné** vyberte své předplatné.
5. V **popřípadě Oblast**vyberte **možnost Východní USA**. Všechny prostředky, které vytvoříte později, musí být ve stejném umístění.
6. Vyberte **zkontrolovat + vytvořit**.
7. Vyberte **Vytvořit**.

Teď vytvořte virtuální síť:

> [!NOTE]
> Velikost podsítě AzureFirewallSubnet je /26. Další informace o velikosti podsítě najdete v [tématu Nejčastější dotazy k bráně Azure Firewall](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. V části **Networking**vyberte **Virtuální síť**.
4. Do **pole Název**zadejte centrum virtuální **sítě**.
5. Do **adresního prostoru**zadejte **10.5.0.0/16**.
6. V části **Předplatné** vyberte své předplatné.
7. Ve **skupině Prostředků**vyberte možnost **FW-Hybrid-Test**.
8. V **pouzdřite možnost Umístění** **vyberte možnost Východní USA**.
9. V části **Podsíť** jako **Název** zadejte **AzureFirewallSubnet**. Brána firewall bude v této podsíti a název podsítě **musí** být AzureFirewallSubnet.
10. Do **oblasti Adres**zadejte **10.5.0.0/26**. 
11. Přijměte další výchozí nastavení a pak vyberte **Vytvořit**.

## <a name="create-the-spoke-virtual-network"></a>Vytvoření virtuální sítě paprsku

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. V části **Networking**vyberte **Virtuální síť**.
4. Do **pole Název**zadejte příkaz **VNet-Spoke**.
5. Do **adresního prostoru**zadejte **10.6.0.0/16**.
6. V části **Předplatné** vyberte své předplatné.
7. Ve **skupině Prostředků**vyberte možnost **FW-Hybrid-Test**.
8. V části **Umístění** vyberte dříve použité umístění.
9. V části **Podsíť** jako **Název** zadejte **SN-Workload**.
10. Do **oblasti Adres**zadejte **10.6.0.0/24**.
11. Přijměte další výchozí nastavení a pak vyberte **Vytvořit**.

## <a name="create-the-on-premises-virtual-network"></a>Vytvoření místní virtuální sítě

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. V části **Networking**vyberte **Virtuální síť**.
4. Do **pole Název**zadejte příkaz **VNet-OnPrem**.
5. Jako **Adresní prostor** zadejte **192.168.0.0/16**.
6. V části **Předplatné** vyberte své předplatné.
7. Ve **skupině Prostředků**vyberte možnost **FW-Hybrid-Test**.
8. V části **Umístění** vyberte dříve použité umístění.
9. V **části Podsíť**v části **Name** type **SN-Corp**.
10. Jako **Rozsah adres** zadejte **192.168.1.0/24**.
11. Přijměte další výchozí nastavení a pak vyberte **Vytvořit**.

Nyní vytvořte druhou podsíť pro bránu.

1. Na stránce **VNet-Onprem** vyberte **Podsítě**.
2. Vyberte **možnost +Podsíť**.
3. Do **pole Název**zadejte **GatewaySubnet**.
4. Pro **rozsah adres (blok CIDR)** typ **192.168.2.0/24**.
5. Vyberte **OK**.

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Toto je veřejná IP adresa používaná pro místní bránu.

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. Do vyhledávacího textového pole zadejte **veřejnou IP adresu** a stiskněte **Enter**.
3. Vyberte **Veřejná IP adresa** a pak vyberte **Vytvořit**.
4. Pro název zadejte **VNet-Onprem-GW-pip**.
5. Pro skupinu prostředků zadejte **FW-Hybrid-Test**.
6. V části **Umístění** vyberte dříve použité umístění.
7. Přijměte ostatní výchozí hodnoty a pak vyberte **Vytvořit**.

## <a name="configure-and-deploy-the-firewall"></a>Konfigurace a nasazení brány firewall

Nyní nasaďte bránu firewall do virtuální sítě centra firewall.

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. V levém sloupci vyberte **Síť**a pak vyberte **brána firewall**.
4. Na stránce **Vytvoření brány firewall** nakonfigurujte bránu firewall podle následující tabulky:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Předplatné     |\<Vaše předplatné\>|
   |Skupina prostředků     |**FW-Hybridní test** |
   |Name (Název)     |**AzFW01**|
   |Umístění     |Vyberte dříve použité umístění.|
   |Volba virtuální sítě     |**Použít existující**:<br> **Rozbočovač virtuální sítě**|
   |Veřejná IP adresa     |Create new (Vytvořit novou): <br>**Název** - **fw-pip**. |

5. Vyberte **Zkontrolovat a vytvořit**.
6. Zkontrolujte souhrn a pak vyberte **Vytvořit,** chcete-li vytvořit bránu firewall.

   Nasazení trvá několik minut.
7. Po dokončení nasazení přejděte do skupiny prostředků **FW-Hybrid-Test** a vyberte bránu firewall **AzFW01.**
8. Poznamenejte si privátní IP adresu. Budete ji potřebovat později při vytváření výchozí trasy.

### <a name="configure-network-rules"></a>Konfigurace pravidel sítě

Nejprve přidejte síťové pravidlo, které umožní webový provoz.

1. Na stránce **AzFW01** vyberte **pravidla**.
2. Vyberte kartu **Kolekce síťových pravidel.**
3. Vyberte **Přidat kolekci síťových pravidel**.
4. Do **pole Název**zadejte **rcnet01**.
5. V **případě priority**zadejte **100**.
6. V části **Akce** vyberte **Povolit**.
6. V části **Pravidla**zadejte **příkaz Název** **příkaz AllowWeb**.
7. V části **Protokol** vyberte **TCP**.
8. V **yberte Zdrojový typ**vyberte adresu **IP**.
9. **Do pole Zdroj**zadejte **192.168.1.0/24**.
10. Do **cílové adresy**zadejte **10.6.0.0/16**
11. Pro **cílové porty**zadejte **80**.

Nyní přidejte pravidlo, které povolí provoz v rdp.

Na druhém řádku pravidla zadejte následující informace:

1. **Název**zadejte **AllowRDP**.
2. V části **Protokol** vyberte **TCP**.
3. V **yberte Zdrojový typ**vyberte adresu **IP**.
4. **Do pole Zdroj**zadejte **192.168.1.0/24**.
5. Do **cílové adresy**zadejte **10.6.0.0/16**
6. Pro **cílové porty**zadejte **3389**.
7. Vyberte **Přidat**.

## <a name="create-and-connect-the-vpn-gateways"></a>Vytvoření a propojení bran VPN

Rozbočovač a místní virtuální sítě jsou propojené přes brány VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Vytvoření brány VPN pro virtuální síť rozbočovače

Teď vytvořte bránu VPN pro virtuální síť rozbočovače. Konfigurace sítě-sítě vyžadují RouteBased VpnType. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. Do vyhledávacího textového pole zadejte **bránu virtuální sítě** a stiskněte **Enter**.
3. Vyberte **Brána virtuální sítě**a vyberte **Vytvořit**.
4. Do **pole Název**zadejte **gw-hub**.
5. V **části Oblast**vyberte stejnou oblast, kterou jste použili dříve.
6. V **části Typ brány**vyberte možnost **VPN**.
7. V **případě typu VPN**vyberte možnost **Route-based**.
8. V **části Skladová položka**vyberte **položku Základní**.
9. V **případě virtuální sítě**vyberte **vyžadované virtuální sítě**.
10. V **části Veřejná IP adresa**vyberte Vytvořit **nový**a zadejte název **VNet-hub-GW-pip.**
11. Přijměte zbývající výchozí hodnoty a pak vyberte **Zkontrolovat + vytvořit**.
12. Zkontrolujte konfiguraci a vyberte **Vytvořit**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Vytvoření brány VPN pro místní virtuální síť

Teď vytvořte bránu VPN pro místní virtuální síť. Konfigurace sítě-sítě vyžadují RouteBased VpnType. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. Do vyhledávacího textového pole zadejte **bránu virtuální sítě** a stiskněte **Enter**.
3. Vyberte **Brána virtuální sítě**a vyberte **Vytvořit**.
4. Do **pole Název**zadejte příkaz **GW-Onprem**.
5. V **části Oblast**vyberte stejnou oblast, kterou jste použili dříve.
6. V **části Typ brány**vyberte možnost **VPN**.
7. V **případě typu VPN**vyberte možnost **Route-based**.
8. V **části Skladová položka**vyberte **položku Základní**.
9. V **případě virtuální sítě**vyberte možnost **VNet-Onprem**.
10. V **části Veřejná IP adresa**vyberte Vytvořit **nový**a zadejte název **VNet-Onprem-GW-pip.**
11. Přijměte zbývající výchozí hodnoty a pak vyberte **Zkontrolovat + vytvořit**.
12. Zkontrolujte konfiguraci a vyberte **Vytvořit**.

### <a name="create-the-vpn-connections"></a>Vytvoření připojení VPN

Nyní můžete vytvořit připojení VPN mezi rozbočovačem a místními bránami.

V tomto kroku vytvoříte připojení z virtuální sítě rozbočovače k místní virtuální síti. Zobrazí se sdílený klíč uváděný v příkladech. Pro sdílený klíč můžete použít vlastní hodnoty. Důležité je, že se sdílený klíč pro obě připojení musí shodovat. Vytvoření připojení může nějakou dobu trvat.

1. Otevřete skupinu prostředků **FW-Hybrid-Test** a vyberte bránu **GW-hub.**
2. V levém sloupci vyberte **Připojení.**
3. Vyberte **Přidat**.
4. Název připojení zadejte **Hub-to-Onprem**.
5. Vyberte **virtuální síť k virtuální síti** pro typ **připojení**.
6. Pro **druhou bránu virtuální sítě**vyberte **GW-Onprem**.
7. Pro **sdílený klíč (PSK)** zadejte **AzureA1b2C3**.
8. Vyberte **OK**.

Vytvořte místní připojení k rozbočovači virtuální sítě. Tento krok je podobný předchozímu kroku, s výjimkou vytvoření připojení z virtuální sítě-onprem do centra virtuální sítě. Ověřte, že se sdílené klíče shodují. Připojení se vytvoří během několika minut.

1. Otevřete skupinu prostředků **FW-Hybrid-Test** a vyberte bránu **GW-Onprem.**
2. V levém sloupci vyberte **Připojení.**
3. Vyberte **Přidat**.
4. Název připojení zadejte **Onprem-to-Hub**.
5. Vyberte **virtuální síť k virtuální síti** pro typ **připojení**.
6. Pro **druhou bránu virtuální sítě**vyberte **GW-hub**.
7. Pro **sdílený klíč (PSK)** zadejte **AzureA1b2C3**.
8. Vyberte **OK**.


#### <a name="verify-the-connection"></a>Ověření připojení

Asi po pěti minutách by měl být stav obou připojení **připojen**.

![Připojení brány](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Vzájemné připojení virtuálních sítí rozbočovače a paprsků

Nyní peer rozbočovač a mluvil virtuální sítě.

1. Otevřete skupinu prostředků **FW-Hybrid-Test** a vyberte virtuální síť **virtuálního rozbočovače.**
2. V levém sloupci vyberte **Partnerské partnerské partnerské partnerské partnerské partnerské strany**.
3. Vyberte **Přidat**.
4. Do **pole Název**zadejte **HubtoSpoke**.
5. Pro **virtuální síť**vyberte **virtuální síť-spoke**
6. Pro název partnerského vztahu z Virtuální sítě do centra virtuální sítě zadejte **SpoketoHub**.
7. Vyberte **Povolit přenos brány**.
8. Vyberte **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Konfigurace dalších nastavení pro partnerský vztah SpoketoHub

Budete muset povolit **povolit přepojování provozu** na partnerského vztahu SpoketoHub.

1. Otevřete skupinu prostředků **FW-Hybrid-Test** a vyberte virtuální síť **S virtuální sítí.**
2. V levém sloupci vyberte **Partnerské partnerské partnerské partnerské partnerské partnerské strany**.
3. Vyberte partnerský vztah **SpoketoHub.**
4. V části **Povolit předávaný provoz z centra virtuální sítě na virtuální síť -Spoke**vyberte **Povoleno**.
5. Vyberte **Uložit**.

## <a name="create-the-routes"></a>Vytvoření tras

Dále vytvořte několik tras:

- Trasa z podsítě brány rozbočovače do podsítě paprsku přes IP adresu brány firewall
- Výchozí trasa z podsítě paprsku přes IP adresu brány firewall

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. Do vyhledávacího textového pole zadejte **směrovací tabulku** a stiskněte **Enter**.
3. Vyberte **možnost Směrovat tabulku**.
4. Vyberte **Vytvořit**.
5. Pro název zadejte **UDR-Hub-Spoke**.
6. Vyberte **FW-Hybrid-Test** pro skupinu prostředků.
8. V části **Umístění** vyberte dříve použité umístění.
9. Vyberte **Vytvořit**.
10. Po vytvoření směrovací tabulky ji vyberte, chcete-li otevřít stránku tabulky trasy.
11. V levém sloupci vyberte **Trasy.**
12. Vyberte **Přidat**.
13. Pro název trasy zadejte **ToSpoke**.
14. Pro předponu adresy zadejte **10.6.0.0/16**.
15. Pro další typ směrování vyberte **virtuální zařízení**.
16. Pro další adresu směrování zadejte privátní IP adresu brány firewall, kterou jste si poznamenali dříve.
17. Vyberte **OK**.

Nyní přidružte trasu k podsíti.

1. Na stránce **UDR-Hub-Spoke - Routes** vyberte **Podsítě**.
2. Vyberte **Přidružit**.
3. Vyberte **Zvolit virtuální síť**.
4. Vyberte **centrum virtuální sítě**.
5. Vyberte **položku GatewaySubnet**.
6. Vyberte **OK**.

Nyní vytvořte výchozí trasu z podsítě paprsku.

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. Do vyhledávacího textového pole zadejte **směrovací tabulku** a stiskněte **Enter**.
3. Vyberte **možnost Směrovat tabulku**.
5. Vyberte **Vytvořit**.
6. Pro název zadejte **UDR-DG**.
7. Vyberte **FW-Hybrid-Test** pro skupinu prostředků.
8. V části **Umístění** vyberte dříve použité umístění.
4. V **části Šíření postupu směrování brány virtuální sítě**vyberte Možnost **Zakázáno**.
1. Vyberte **Vytvořit**.
2. Po vytvoření směrovací tabulky ji vyberte, chcete-li otevřít stránku tabulky trasy.
3. V levém sloupci vyberte **Trasy.**
4. Vyberte **Přidat**.
5. Pro název trasy zadejte **ToHub**.
6. Pro předponu adresy zadejte **0.0.0.0/0**.
7. Pro další typ směrování vyberte **virtuální zařízení**.
8. Pro další adresu směrování zadejte privátní IP adresu brány firewall, kterou jste si poznamenali dříve.
9. Vyberte **OK**.

Nyní přidružte trasu k podsíti.

1. Na stránce **UDR-DG - Trasy** vyberte **Podsítě**.
2. Vyberte **Přidružit**.
3. Vyberte **Zvolit virtuální síť**.
4. Vyberte **virtuální síť paprsku**.
5. Vyberte **možnost Úloha SN**.
6. Vyberte **OK**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Teď vytvořte zatížení paprsku a místní virtuální počítače a umístěte je do příslušných podsítí.

### <a name="create-the-workload-virtual-machine"></a>Vytvoření virtuálního počítače úloh

Vytvořte virtuální počítač ve virtuální síti s paprsky se službou IIS bez veřejné IP adresy.

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. V části **Oblíbené**vyberte **Windows Server 2016 Datacenter**.
3. Zadejte pro virtuální počítač tyto hodnoty:
    - **Skupina prostředků** – vyberte **FW-Hybrid-Test**.
    - **Název virtuálního počítače**: *VM-Spoke-01*.
    - **Oblast** – stejná oblast, ve které jste se dříve používali.
    - **Uživatelské jméno**: *azureuser*.
    - **Heslo:** *Azure123456!*
4. Vyberte **Další:Disky**.
5. Přijměte výchozí hodnoty a vyberte **možnost Další: Síť**.
6. Vyberte **virtuální síť-Spoke** pro virtuální síť a podsíť je **SN-Workload**.
7. V **případě veřejné IP adresy**vyberte možnost **Žádný**.
8. U **veřejných příchozích portů**vyberte **Povolit vybrané porty**a pak vyberte **HTTP (80)** a **RDP (3389).**
9. Vyberte **Další:Správa**.
10. Pro **diagnostiku spuštění**vyberte **možnost Vypnuto**.
11. Vyberte **Zkontrolovat+Vytvořit**, zkontrolujte nastavení na stránce souhrnu a pak vyberte **Vytvořit**.

### <a name="install-iis"></a>Instalace služby IIS

1. Na webu Azure otevřete Cloud Shell a ujistěte se, že je nastavený na **PowerShell**.
2. Spusťte následující příkaz pro instalaci služby IIS do virtuálního počítače a v případě potřeby změňte umístění:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>Vytvoření místního virtuálního počítače

Jedná se o virtuální počítač, který slouží k připojení pomocí vzdálené plochy k veřejné IP adrese. Odtud se pak připojíte k místnímu serveru přes bránu firewall.

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. V části **Oblíbené**vyberte **Windows Server 2016 Datacenter**.
3. Zadejte pro virtuální počítač tyto hodnoty:
    - **Skupina prostředků** – vyberte existující a pak vyberte **FW-Hybrid-Test**.
    - **Název virtuálního počítače** - *VM-Onprem*.
    - **Oblast** – stejná oblast, ve které jste se dříve používali.
    - **Uživatelské jméno**: *azureuser*.
    - **Heslo:** *Azure123456!*.
4. Vyberte **Další:Disky**.
5. Přijměte výchozí hodnoty a vyberte **Další:Síť**.
6. Vyberte **virtuální síť-onprem** pro virtuální síť a podsíť je **SN-Corp**.
7. V **části Veřejné příchozí porty**vyberte **Povolit vybrané porty**a pak vyberte **možnost RDP (3389).**
8. Vyberte **Další:Správa**.
9. Pro **diagnostiku spuštění**vyberte **možnost Vypnuto**.
10. Vyberte **Zkontrolovat+Vytvořit**, zkontrolujte nastavení na stránce souhrnu a pak vyberte **Vytvořit**.

## <a name="test-the-firewall"></a>Testování brány firewall

1. Nejprve si všimněte privátní IP adresy **virtuálního počítače s paprsky-01.**

2. Na webu Azure Portal se připojte k virtuálnímu počítači **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Otevřete webový prohlížeč na **VM-Onprem**\<a přejděte na http://\>soukromé IP adresy VM-spoke-01 .

   Měli byste vidět webovou stránku **VM-spoke-01:** ![Webová stránka VM-Spoke-01](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. Z virtuálního počítače **VM-Onprem** otevřete vzdálenou plochu na **VM-spoke-01** na privátní IP adresu.

   Připojení by mělo být úspěšné a měli byste být schopni se přihlásit.

Takže teď jste ověřili, že pravidla brány firewall fungují:

<!---- You can ping the server on the spoke VNet.--->
- Webový server můžete procházet ve virtuální síti s paprsky.
- K serveru ve virtuální síti s paprsky se můžete připojit pomocí programu RDP.

Dále změňte akci kolekce pravidel sítě brány firewall na **Odepřít**, abyste ověřili, že pravidla brány firewall fungují podle očekávání.

1. Vyberte bránu firewall **AzFW01.**
2. Vyberte **Pravidla**.
3. Vyberte kartu **Kolekce síťových pravidel** a vyberte kolekci pravidel **RCNet01.**
4. V **akci**vyberte **možnost Odepřít**.
5. Vyberte **Uložit**.

Před testováním změněných pravidel ukončete všechna existující připojení vzdálené plochy. Teď znovu spusťte testy. Tentokrát by všechny měly selhat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky brány firewall si můžete ponechat pro další kurz, nebo můžete odstraněním skupiny prostředků **FW-Hybrid-Test** odstranit všechny prostředky související z bránou firewall, pokud už je nepotřebujete.

## <a name="next-steps"></a>Další kroky

Dál můžete pokračovat monitorováním protokolů brány Azure Firewall.

> [!div class="nextstepaction"]
> [Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
