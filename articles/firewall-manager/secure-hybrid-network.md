---
title: 'Kurz: zabezpečení služby hub Virtual Network pomocí správce Azure Firewall'
description: V tomto kurzu se naučíte zabezpečit svou virtuální síť pomocí Azure Firewall Manageru pomocí Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: victorh
ms.openlocfilehash: f631100003a4ea6e191a5bdc13a11eb9aa327268
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212534"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager"></a>Kurz: zabezpečení služby hub Virtual Network pomocí správce Azure Firewall

Když připojíte místní síť k virtuální síti Azure a vytvoříte hybridní síť, bude mít možnost řídit přístup k síťovým prostředkům Azure důležitou součást celkového plánu zabezpečení.

Pomocí správce Azure Firewall můžete vytvořit virtuální síť centrální sítě, která zajistí zabezpečení hybridních síťových přenosů určených na privátní IP adresy, Azure PaaS a Internet. Nástroj Azure Firewall Manager můžete použít k řízení přístupu k síti v hybridní síti pomocí zásad, které definují povolený a zakázaný síťový provoz.

Správce brány firewall taky podporuje zabezpečenou architekturu virtuálního rozbočovače. Porovnání typů architektury zabezpečeného virtuálního centra a centra virtuálních sítí najdete v tématu [co jsou možnosti architektury Azure firewall Manageru?](vhubs-and-vnets.md)

Pro tento kurz vytvoříte tři virtuální sítě:

- **VNet-hub** – brána firewall je v této virtuální síti.
- **VNet-paprsek** – virtuální síť paprsků představuje zatížení, které je umístěné v Azure.
- **VNet-OnPrem** -místní virtuální síť představuje místní síť. Ve skutečném nasazení je možné ho připojit buď pomocí sítě VPN, nebo připojení ExpressRoute. Pro zjednodušení tento kurz používá připojení brány VPN a k reprezentaci místní sítě se používá virtuální síť, která je umístěná v Azure.

![Hybridní síť](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zásady brány firewall
> * Vytvoření virtuálních sítí
> * Konfigurace a nasazení brány firewall
> * Vytvoření a propojení bran VPN
> * Vytvoření partnerského vztahu mezi virtuálními sítěmi hub a paprsek
> * Vytvoření tras
> * Vytvoření virtuálních počítačů
> * Testování brány firewall


## <a name="prerequisites"></a>Požadavky

Hybridní síť používá model architektury hvězdicové a hvězdicové ke směrování provozu mezi Azure virtuální sítě a místními sítěmi. Architektura centra a paprsků má následující požadavky:

- Nastavte **AllowGatewayTransit** při VNet-Hub partnerských vztahů na VNet-paprsek. V architektuře sítě s rozbočovačem a paprsky umožňuje přenos brány virtuální sítě rozbočovače sdílení brány VPN v centru místo nasazení bran VPN v každé virtuální síti paprsků. 

   Trasy k virtuálním sítím připojeným bránou nebo místním sítím se navíc automaticky rozšíří do směrovacích tabulek pro partnerské virtuální sítě s použitím přenosu brány. Další informace najdete v tématu [Konfigurace přenosu brány VPN pro partnerský vztah virtuálních sítí](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Nastavte **useremotegateways nastavený** , když se VNet-Spoke partnerských vztahů k virtuálnímu centru VNET. Pokud je nastavená taky **useremotegateways nastavený** a **AllowGatewayTransit** ve vzdáleném partnerském vztahu, virtuální síť paprsků používá pro přenos brány vzdálenou virtuální síť.
- Chcete-li směrovat přenos podsítě paprsků přes bránu firewall centra, potřebujete trasu definovanou uživatelem (UDR), která odkazuje na bránu firewall s nastavením **šíření trasy brány virtuální sítě** zakázané. Tato možnost zabrání distribuci tras do podsítí paprsků. Tím se zabrání tomu, aby se naučily trasy z konfliktu s vaším UDR.
- Nakonfigurujte UDR v podsíti brány centra, která odkazuje na IP adresu brány firewall jako na další segment směrování sítí paprsků. V Azure Firewall podsíti se nevyžadují žádné UDR, protože se učí trasy od protokolu BGP.

Postup vytvoření těchto tras najdete v části [Vytvoření pravidel](#create-the-routes) v tomto kurzu.

>[!NOTE]
>Služba Azure Firewall musí mít přímé připojení k internetu. Pokud vaše AzureFirewallSubnet zjišťuje výchozí trasu k místní síti přes protokol BGP, musíte tuto hodnotu přepsat hodnotou 0.0.0.0/0 UDR s hodnotou **typem** nastavenou jako **Internet** pro udržování přímého připojení k Internetu.
>
>Azure Firewall lze nakonfigurovat pro podporu vynuceného tunelování. Další informace najdete v tématu [Azure firewall vynucené tunelování](../firewall/forced-tunneling.md).

>[!NOTE]
>Provoz mezi přímo rovnocenným virtuální sítě je směrován přímo, i když jako výchozí bránu UDR body Azure Firewall. Aby bylo možné odeslat podsíť do brány firewall v tomto scénáři, musí UDR v obou podsítích explicitně obsahovat předponu sítě cílové podsítě.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-firewall-policy"></a>Vytvoření zásady brány firewall

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na panelu hledání Azure Portal zadejte **Správce brány firewall** a stiskněte klávesu **ENTER**.
3. Na stránce správce Azure Firewall vyberte **Zobrazit zásady brány firewall Azure**.

   ![Zásady brány firewall](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Vyberte **vytvořit Azure firewall zásady**.
1. Vyberte své předplatné a v případě skupiny prostředků vyberte **vytvořit novou** a vytvořte skupinu prostředků s názvem **FW-Hybrid-test**.
2. Jako název zásady zadejte **Pol-Net01**.
3. V oblasti oblast vyberte **východní USA**.
1. Vyberte **Další: nastavení DNS**.
1. Vyberte **Další: Kontrola TLS (Preview).**
1. Vyberte **Další: pravidla**.
1. Vyberte **přidat kolekci pravidel**.
1. Jako **název** zadejte **RCNet01**.
1. Jako **typ kolekce pravidel** vyberte **síť**.
1. Jako **Priorita** zadejte **100**.
1. V části **Akce** vyberte **Povolit**.
1. Do pole **pravidla** zadejte  **AllowWeb**.
1. Jako **zdroj** zadejte **192.168.1.0/24**.
1. V části **Protokol** vyberte **TCP**.
1. V případě **cílových portů** zadejte **80**.
1. Jako **typ cíle** vyberte **IP adresa**.
1. Jako **cíl** zadejte **10.6.0.0/16**.
1. Na řádku Next Rule (další pravidlo) zadejte následující informace:
 
    Název: typ **AllowRDP**<br>
    Zdroj: typ **192.168.1.0/24**.<br>
    Protokol, vyberte **TCP**<br>
    Cílové porty zadejte **3389**<br>
    Cílový typ, vyberte **IP adresa** .<br>
    Pro cíl zadejte **10.6.0.0/16** .

1. Vyberte **Přidat**.
2. Vyberte **Zkontrolovat a vytvořit**.
3. Zkontrolujte podrobnosti a pak vyberte **vytvořit**.

## <a name="create-the-firewall-hub-virtual-network"></a>Vytvoření virtuální sítě centra firewallu

> [!NOTE]
> Velikost podsítě AzureFirewallSubnet je/26. Další informace o velikosti podsítě najdete v tématu [Azure firewall Nejčastější dotazy](../firewall/firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Vyhledejte **virtuální síť** a pak vyberte **virtuální síť**.
1. Vyberte **Vytvořit**.
1. V části **Předplatné** vyberte své předplatné.
1. Jako **skupinu prostředků** vyberte **FW-Hybrid-test**.
1. Jako **název** zadejte **VNet-hub**.
1. V **oblasti oblast** vyberte **východní USA**.
1. Vyberte **Další: IP adresy**.

1. V případě **adresního prostoru IPv4** zadejte **10.5.0.0/16**.
1. V části **název podsítě** vyberte **výchozí**.
1.  Změňte **název podsítě** na **AzureFirewallSubnet**. Brána firewall je v této podsíti a název podsítě **musí** být AzureFirewallSubnet.
1. Jako **Rozsah adres podsítě** zadejte **10.5.0.0/26**. 
1. Přijměte ostatní výchozí nastavení a pak vyberte **Uložit**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

## <a name="create-the-spoke-virtual-network"></a>Vytvoření virtuální sítě paprsků

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Vyhledejte **virtuální síť** a pak vyberte **virtuální síť**.
1. Vyberte **Vytvořit**.
1. V části **Předplatné** vyberte své předplatné.
1. Jako **skupinu prostředků** vyberte **FW-Hybrid-test**.
1. Jako **název** zadejte **VNet-paprsek**.
1. V **oblasti oblast** vyberte **východní USA**.
1. Vyberte **Další: IP adresy**.

1. V případě **adresního prostoru IPv4** zadejte **10.6.0.0/16**.
1. V části **název podsítě** vyberte **výchozí**.
1. Změňte **název podsítě** na **sériové zatížení**.
1. Jako **Rozsah adres podsítě** zadejte **10.6.0.0/24**. 
1. Přijměte ostatní výchozí nastavení a pak vyberte **Uložit**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.


## <a name="create-the-on-premises-virtual-network"></a>Vytvoření místní virtuální sítě

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Vyhledejte **virtuální síť** a pak vyberte **virtuální síť**.
1. Vyberte **Vytvořit**.
1. V části **Předplatné** vyberte své předplatné.
1. Jako **skupinu prostředků** vyberte **FW-Hybrid-test**.
1. Jako **název** zadejte **VNet-OnPrem**.
1. V **oblasti oblast** vyberte **východní USA**.
1. Vyberte **Další: IP adresy**.

1. V případě **adresního prostoru IPv4** zadejte **192.168.0.0/16**.
1. V části **název podsítě** vyberte **výchozí**.
1. Změňte **název podsítě** na **sn-Corp**.
1. Jako **Rozsah adres podsítě** zadejte **192.168.1.0/24**. 
1. Přijměte ostatní výchozí nastavení a pak vyberte **Uložit**.
2. Vyberte **Přidat podsíť**.
3. Jako **název podsítě** zadejte **GatewaySubnet**.
4. Pro **Rozsah adres podsítě** typu **192.168.2.0/24**.
5. Vyberte **Přidat**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.




## <a name="configure-and-deploy-the-firewall"></a>Konfigurace a nasazení brány firewall

Pokud jsou zásady zabezpečení přidružené k rozbočovači, označuje se jako *virtuální síť rozbočovače*.

Převeďte virtuální síť **rozbočovače VNet** na *virtuální síť rozbočovače* a zabezpečte ji pomocí Azure firewall.

1. Na panelu hledání Azure Portal zadejte **Správce brány firewall** a stiskněte klávesu **ENTER**.
3. Na stránce správce Azure Firewall v části **Přidat zabezpečení do virtuálních sítí** vyberte **Zobrazit rozbočovač virtuální sítě**.
1. V části **virtuální sítě** zaškrtněte políčko u pole **VNet-hub**.
1. Vyberte **Spravovat zabezpečení** a pak vyberte **nasadit bránu firewall se zásadami brány firewall**.
1. Na stránce **převést virtuální sítě** v části **zásady brány firewall** zaškrtněte políčko pro **Pol-Net01**.
1. Vybrat **Další: zkontrolovat + potvrdit**
1. Zkontrolujte podrobnosti a pak vyberte **Potvrdit**.


   Nasazení bude trvat několik minut.
7. Po dokončení nasazení přejdete do skupiny prostředků **FW-Hybrid-test** a vyberete bránu firewall.
9. Poznamenejte si **privátní IP adresu brány firewall** na stránce **Přehled** . Budete ji potřebovat později při vytváření výchozí trasy.

## <a name="create-and-connect-the-vpn-gateways"></a>Vytvoření a propojení bran VPN

Rozbočovač a místní virtuální sítě se připojují prostřednictvím bran VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Vytvoření brány VPN pro virtuální síť centrální sítě

Nyní vytvořte bránu VPN pro virtuální síť centrální sítě. Konfigurace sítě na síť vyžadují RouteBased VpnType. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Do textového pole Hledat zadejte **bránu virtuální sítě** a stiskněte klávesu **ENTER**.
3. Vyberte **Brána virtuální sítě** a vyberte **vytvořit**.
4. Jako **název** zadejte **GS-hub**.
5. V **oblasti oblast** vyberte **(US) východní USA**.
6. Jako **typ brány** vyberte **VPN**.
7. Jako **typ sítě VPN** vyberte **směrování založené na trasách**.
8. V případě **SKU** vyberte **základní**.
9. V případě služby **Virtual Network** vyberte **VNet-hub**.
10. Pro **veřejnou IP adresu** vyberte **vytvořit novou** a jako název zadejte **VNet-hub-GS-PIP** .
11. Potvrďte zbývající výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.
12. Zkontrolujte konfiguraci a pak vyberte **vytvořit**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Vytvoření brány VPN pro místní virtuální síť

Teď vytvořte bránu VPN pro místní virtuální síť. Konfigurace sítě na síť vyžadují RouteBased VpnType. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Do textového pole Hledat zadejte **bránu virtuální sítě** a stiskněte klávesu **ENTER**.
3. Vyberte **Brána virtuální sítě** a vyberte **vytvořit**.
4. Jako **název** zadejte **GS-OnPrem**.
5. V **oblasti oblast** vyberte **(US) východní USA**.
6. Jako **typ brány** vyberte **VPN**.
7. Jako **typ sítě VPN** vyberte **směrování založené na trasách**.
8. V případě **SKU** vyberte **základní**.
9. V případě služby **Virtual Network** vyberte **VNet-OnPrem**.
10. Pro **veřejnou IP adresu** vyberte **vytvořit novou** a jako název zadejte **VNet-OnPrem-GS-PIP** .
11. Potvrďte zbývající výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.
12. Zkontrolujte konfiguraci a pak vyberte **vytvořit**.

### <a name="create-the-vpn-connections"></a>Vytvoření připojení VPN

Nyní můžete vytvořit připojení VPN mezi centrem a místními bránami.

V tomto kroku vytvoříte připojení z virtuální sítě rozbočovače k místní virtuální síti. Zobrazí se sdílený klíč uváděný v příkladech. Pro sdílený klíč můžete použít vlastní hodnoty. Důležité je, že se sdílený klíč pro obě připojení musí shodovat. Vytvoření připojení trvá nějakou dobu.

1. Otevřete skupinu prostředků- **Hybrid-test FW** a vyberte bránu **GS-hub** .
2. V levém sloupci vyberte **připojení** .
3. Vyberte **Přidat**.
4. Do pole název připojení zadejte **hub-to-OnPrem**.
5. Pro **Typ připojení** vyberte **VNet-to-VNet** .
6. Pro **druhou bránu virtuální sítě** vyberte **GS-OnPrem**.
7. Pro **sdílený klíč (PSK)** zadejte **AzureA1b2C3**.
8. Vyberte **OK**.

Vytvořte připojení k virtuální síti z místního prostředí k rozbočovači. Tento krok je podobný předchozímu, s tím rozdílem, že vytvoříte připojení z VNet-Onprem k rozbočovači VNet. Ověřte, že se sdílené klíče shodují. Připojení se vytvoří během několika minut.

1. Otevřete skupinu prostředků " **Hybrid-test FW** " a vyberte bránu **GS-OnPrem** .
2. V levém sloupci vyberte **připojení** .
3. Vyberte **Přidat**.
4. Název připojení zadejte **OnPrem-to-hub**.
5. Pro **Typ připojení** vyberte **VNet-to-VNet** .
6. Pro **druhou bránu virtuální sítě** vyberte **GS-hub**.
7. Pro **sdílený klíč (PSK)** zadejte **AzureA1b2C3**.
8. Vyberte **OK**.


#### <a name="verify-the-connection"></a>Ověření připojení

Po asi pěti minutách se musí **připojit** stav obou připojení.

![Připojení brány](media/secure-hybrid-network/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Vytvoření partnerského vztahu mezi virtuálními sítěmi hub a paprsek

Nyní můžete vytvořit partnerský vztah mezi virtuálními sítěmi hub a paprsek.

1. Otevřete skupinu prostředků " **Hybrid-test FW** " a vyberte virtuální síť **centra VNet** .
2. V levém sloupci vyberte **partnerské vztahy**.
3. Vyberte **Přidat**.
4. V rámci **této virtuální sítě**:
 
   
   |Název nastavení  |Hodnota  |
   |---------|---------|
   |Název propojení partnerského vztahu| HubtoSpoke|
   |Provoz do vzdálené virtuální sítě|   Povoleno (výchozí)      |
   |Přenos předaných ze vzdálené virtuální sítě    |   Povoleno (výchozí)      |
   |Brána virtuální sítě nebo server tras    |  Použít bránu této virtuální sítě       |
    
5. V části **Vzdálená virtuální síť**:

   |Název nastavení  |Hodnota  |
   |---------|---------|
   |Název propojení partnerského vztahu | SpoketoHub|
   |Model nasazení virtuální sítě| Resource Manager|
   |Předplatné|\<your subscription\>|
   |Virtuální síť| VNet-Spoke
   |Provoz do vzdálené virtuální sítě     |   Povoleno (výchozí)      |
   |Přenos předaných ze vzdálené virtuální sítě    |   Povoleno (výchozí)      |
   |Brána virtuální sítě     |  Použít bránu vzdálené virtuální sítě       |

5. Vyberte **Přidat**.

   :::image type="content" source="media/secure-hybrid-network/firewall-peering.png" alt-text="Partnerský vztah virtuální sítě":::

## <a name="create-the-routes"></a>Vytvoření tras

Dále vytvořte několik tras:

- Trasa z podsítě brány rozbočovače do podsítě paprsku přes IP adresu brány firewall
- Výchozí trasa z podsítě paprsku přes IP adresu brány firewall

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Do textového pole Hledat zadejte **Směrování Table** a stiskněte klávesu **ENTER**.
3. Vyberte **směrovací tabulku**.
4. Vyberte **Vytvořit**.
1. Vyberte položku **FW-Hybrid-test** pro skupinu prostředků.
1. V **oblasti oblast** vyberte **východní USA**.
1. Jako název zadejte **udr-hub-paprsek**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.
1. Po vytvoření směrovací tabulky vyberte ji a otevřete stránku směrovací tabulka.
1. V levém sloupci vyberte možnost **trasy** .
1. Vyberte **Přidat**.
1. Jako název trasy zadejte **ToSpoke**.
1. Jako předponu adresy zadejte **10.6.0.0/16**.
1. V poli Typ dalšího segmentu směrování vyberte **virtuální zařízení**.
1. Do pole adresa dalšího směrování zadejte privátní IP adresu brány firewall, kterou jste si poznamenali dříve.
1. Vyberte **OK**.

Nyní připojte trasu k podsíti.

1. Na stránce **udr-hub-paprsek-Routes** vyberte **podsítě**.
2. Vyberte **přidružit**.
4. V části **virtuální síť** vyberte **VNet-hub**.
5. V části **podsíť** vyberte **GatewaySubnet**.
6. Vyberte **OK**.

Nyní vytvořte výchozí trasu z podsítě paprsků.

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Do textového pole Hledat zadejte **Směrování Table** a stiskněte klávesu **ENTER**.
3. Vyberte **směrovací tabulku**.
5. Vyberte **Vytvořit**.
7. Vyberte položku **FW-Hybrid-test** pro skupinu prostředků.
8. V **oblasti oblast** vyberte **východní USA**.
1. Jako název zadejte **udr-DG**.
4. V případě **tras pro rozšíření** pro přenos brány vyberte možnost **ne**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.
1. Po vytvoření směrovací tabulky vyberte ji a otevřete stránku směrovací tabulka.
1. V levém sloupci vyberte možnost **trasy** .
1. Vyberte **Přidat**.
1. Jako název trasy zadejte **ToHub**.
1. Jako předponu adresy zadejte **0.0.0.0/0**.
1. V poli Typ dalšího segmentu směrování vyberte **virtuální zařízení**.
1. Do pole adresa dalšího směrování zadejte privátní IP adresu brány firewall, kterou jste si poznamenali dříve.
1. Vyberte **OK**.

Nyní připojte trasu k podsíti.

1. Na stránce **udr-DG-Routes** vyberte **podsítě**.
2. Vyberte **přidružit**.
4. V části **virtuální síť** vyberte **VNet-paprsek**.
5. V části **podsíť** vyberte **sériové – zatížení**.
6. Vyberte **OK**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Teď vytvořte úlohu paprsků a místní virtuální počítače a umístěte je do příslušných podsítí.

### <a name="create-the-workload-virtual-machine"></a>Vytvoření virtuálního počítače úloh

Vytvořte virtuální počítač ve virtuální síti paprsků a spusťte službu IIS bez veřejné IP adresy.

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. V části **Oblíbené** vyberte **Windows Server 2016 Datacenter**.
3. Zadejte pro virtuální počítač tyto hodnoty:
    - **Skupina prostředků** – vyberte **FW-Hybrid-test**
    - **Název virtuálního počítače**: *virtuální počítač-paprskový-01*
    - **Oblast**  -  *(US) východní USA*
    - **Uživatelské jméno**: zadejte uživatelské jméno.
    - **Heslo**: zadejte heslo.

4. Vyberte **Další: disky**.
5. Přijměte výchozí hodnoty a vyberte **Další: sítě**.
6. Vyberte **VNet-hvězdicové** pro virtuální síť a podsíť je **sn-zatížení**.
8. U **veřejných příchozích portů** vyberte **Povolit vybrané porty** a pak vyberte **http (80)** a **RDP (3389)** .
1. Vyberte **Další: Správa**.
1. V případě **diagnostiky spouštění** vyberte **Zakázat**.
1. Vyberte **zkontrolovat + vytvořit**, zkontrolujte nastavení na stránce Souhrn a pak vyberte **vytvořit**.

### <a name="install-iis"></a>Instalace služby IIS

1. Z Azure Portal otevřete Cloud Shell a ujistěte se, že je nastavené na **PowerShell**.
2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuálním počítači a v případě potřeby změňte umístění:

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

Toto je virtuální počítač, který používáte k připojení pomocí vzdálené plochy k veřejné IP adrese. Odtud se pak pomocí brány firewall připojíte k místnímu serveru.

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. V části **Oblíbené** vyberte **Windows Server 2016 Datacenter**.
3. Zadejte pro virtuální počítač tyto hodnoty:
    - **Skupina prostředků** – vyberte existující a pak vyberte **FW-Hybrid-test**
    - **Název**  -  virtuálního počítače *Virtuální počítač – OnPrem*
    - **Oblast**  -  *(US) východní USA*
    - **Uživatelské jméno**: zadejte uživatelské jméno.
    - **Heslo**: zadejte heslo.

4. Vyberte **Další: disky**.
5. Přijměte výchozí hodnoty a vyberte **Další: sítě**.
6. Vyberte **VNet-OnPrem** pro virtuální síť a ověřte, že podsíť je **sn-Corp**.
7. Pro **veřejné příchozí porty** vyberte **Povolit vybrané porty** a pak vyberte **RDP (3389)** .
8. Vyberte **Další: Správa**.
9. V případě **diagnostiky spouštění** vyberte **Zakázat**.
10. Vyberte **zkontrolovat + vytvořit**, zkontrolujte nastavení na stránce Souhrn a pak vyberte **vytvořit**.

## <a name="test-the-firewall"></a>Testování brány firewall

1. Nejdřív si poznamenejte privátní IP adresu virtuálního počítače VM-paprsk-01 na stránce s přehledem virtuálního počítače-paprsk-01.

2. Na webu Azure Portal se připojte k virtuálnímu počítači **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Otevřete webový prohlížeč na **virtuálním počítači-OnPrem** a přejděte na http:// \<VM-spoke-01 private IP\> .

   Měla by se zobrazit webová stránka **VM-paprsek-01** webová stránka ![ VM-paprsk-01.](media/secure-hybrid-network/vm-spoke-01-web.png)

4. Z virtuálního počítače **VM-OnPrem** otevřete vzdálenou plochu **virtuálního počítače-paprsek-01** na privátní IP adrese.

   Připojení by mělo být úspěšné a mělo by být možné se přihlásit.

Takže teď ověříte, že pravidla brány firewall fungují:

<!---- You can ping the server on the spoke VNet.--->
- Webový server můžete procházet ve virtuální síti paprsků.
- Pomocí protokolu RDP se můžete připojit k serveru ve virtuální síti paprsků.

Dále změňte akci kolekce pravidel sítě brány firewall na **Odepřít**, abyste ověřili, že pravidla brány firewall fungují podle očekávání.

1. Otevřete skupinu prostředků " **Hybrid-test FW** " a vyberte zásadu brány firewall **Pol-Net01** .
2. V části **Nastavení** vyberte **kolekce pravidel**.
1. Vyberte kolekci pravidel **RCNet01** .
1. V případě **akce kolekce pravidel** vyberte **Odepřít**.
1. Vyberte **Uložit**.

Před testováním změněných pravidel zavřete všechna existující Vzdálená plocha a prohlížeče na **virtuálním počítači-OnPrem** . Po dokončení aktualizace kolekce pravidel spusťte testy znovu. Všechny by se nemohly připojit k tomuto času.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete zachovat prostředky brány firewall pro další šetření, nebo pokud už je nepotřebujete, odstranit **skupinu prostředků pro** všechny prostředky související s bránou firewall a odstranit ji.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: zabezpečení virtuální sítě WAN pomocí správce Azure Firewall](secure-cloud-network.md)