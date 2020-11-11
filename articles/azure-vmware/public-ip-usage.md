---
title: Jak používat funkci veřejné IP adresy v řešení Azure VMware
description: Tento článek vysvětluje, jak používat funkci veřejné IP adresy ve službě Azure Virtual WAN.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 036ec00077720e9dc3197bf9235bea34b77fb5f4
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517899"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>Jak používat funkci veřejné IP adresy v řešení Azure VMware

Veřejná IP adresa je nová funkce při připojení řešení Azure VMware. Zpřístupňuje prostředky, jako jsou webové servery, virtuální počítače (VM) a hostitele přístupné prostřednictvím veřejné sítě. 

Veřejný přístup k Internetu povolíte dvěma způsoby. 

- Aplikace lze hostovat a publikovat v nástroji Application Gateway Load Balancer pro přenosy HTTP/HTTPS.
- Publikováno prostřednictvím funkcí veřejné IP adresy v Azure Virtual WAN.

V rámci nasazení privátního cloudu řešení Azure VMware se po povolení funkce veřejné IP adresy vytvoří a povolí požadované komponenty s automatizací:

-  Virtual WAN

-  Virtuální centrum sítě WAN s připojením ExpressRoute

-  Azure Firewall služby s veřejnou IP adresou

Tento článek podrobně popisuje, jak můžete používat funkci veřejné IP adresy ve virtuální síti WAN.

## <a name="prerequisites"></a>Požadavky

- Prostředí řešení Azure VMware
- WebServer běžící v prostředí řešení Azure VMware.
- Nový překrývající se rozsah IP adres pro nasazení služby Virtual WAN hub, obvykle a `/24` .

## <a name="reference-architecture"></a>Referenční architektura

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Diagram architektury veřejné IP adresy" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

Diagram architektury znázorňuje webový server hostovaný v prostředí řešení Azure VMware a nakonfigurovaný s privátními IP adresami RFC1918.  Webová služba je zpřístupněna internetovým funkcím veřejné IP adresy virtuální sítě WAN.  Veřejná IP adresa je obvykle cílový překlad adres (NAT) přeložený v Azure Firewall. Pomocí pravidel DNAT se v zásadách brány firewall přeloží požadavky na veřejnou IP adresu na soukromou adresu (webserver) s portem.

Požadavky uživatelů přirazí na bránu firewall na veřejné IP adrese, která je zase přeložena na privátní IP adresu pomocí pravidel DNAT v Azure Firewall. Brána firewall zkontroluje tabulku překladu adres (NAT) a pokud požadavek odpovídá položce, přepošle provoz na přeloženou adresu a port v prostředí řešení Azure VMware.

Webový server obdrží požadavek a odpoví na požadované informace nebo stránku brány firewall a brána firewall přepošle informace uživateli na veřejnou IP adresu.

## <a name="test-case"></a>Testovací případ
V tomto scénáři publikujete webserver IIS na Internet. K publikování webu na veřejné IP adrese použijte funkci veřejné IP adresy v řešení Azure VMware.  Také nakonfigurujete pravidla překladu adres (NAT) pro bránu firewall a přístup k prostředku řešení Azure VMware (virtuální počítače s webovým serverem) s veřejnou IP adresou.

## <a name="deploy-virtual-wan"></a>Nasadit Virtual WAN.

1. Přihlaste se k Azure Portal a pak vyhledejte a vyberte **Řešení Azure VMware**.

1. Vyberte privátní cloud řešení Azure VMware.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Snímek obrazovky privátního cloudu řešení Azure VMware." border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. V části **Spravovat** vyberte možnost **připojení**.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Snímek obrazovky oddílu připojení" border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. Vyberte kartu **Veřejná IP adresa** a pak vyberte **Konfigurovat**.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="Snímek obrazovky, který ukazuje, kde začít konfigurovat veřejnou IP adresu" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. Přijměte výchozí hodnoty nebo je změňte a pak vyberte **vytvořit**.

   - Skupina prostředků virtuální sítě WAN

   - Název virtuální sítě WAN

   - Blok adres virtuálního rozbočovače (pomocí nového rozsahu IP adres, který se nepřekrývá)

   - Počet veřejných IP adres (1-100)

Dokončení nasazení všech součástí trvá přibližně jednu hodinu. Toto nasazení se musí nacházet jenom jednou, aby podporovalo všechny budoucí veřejné IP adresy pro toto prostředí řešení Azure VMware.  

>[!TIP]
>Stav můžete monitorovat z **oznamovací** oblasti. 

## <a name="view-and-add-public-ip-addresses"></a>Zobrazení a přidání veřejných IP adres

Pomocí následujících kroků můžeme kontrolovat a přidávat další veřejné IP adresy.

1. V Azure Portal vyhledejte a vyberte **firewall**.

1. Vyberte nasazenou bránu firewall a potom vyberte **navštívit Manager Azure firewall a nakonfigurujte a spravujte tuto bránu firewall**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Snímek obrazovky, který ukazuje možnost konfigurovat a spravovat bránu firewall" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Vyberte **Zabezpečená virtuální centra** a ze seznamu vyberte virtuální rozbočovač.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Snímek obrazovky s manažerem brány firewall" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. Na stránce virtuální rozbočovač vyberte **Konfigurace veřejné IP** adresy a přidejte další veřejnou IP adresu a pak vyberte **Přidat**. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Snímek obrazovky s postupem přidání konfigurace veřejné IP adresy ve Správci brány firewall" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Zadejte požadovaný počet IP adres a vyberte **Přidat**.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Snímek obrazovky pro přidání zadaného počtu konfigurací veřejných IP adres" border="true":::


## <a name="create-firewall-policies"></a>Vytvoření zásad brány firewall

Po nasazení všech součástí je můžete zobrazit v přidané skupině prostředků. Dalším krokem je přidání zásady brány firewall.

1. V Azure Portal vyhledejte a vyberte **firewall**.

1. Vyberte nasazenou bránu firewall a potom vyberte **navštívit Manager Azure firewall a nakonfigurujte a spravujte tuto bránu firewall**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Snímek obrazovky, který ukazuje možnost konfigurovat a spravovat bránu firewall" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Vyberte **zásady Azure firewall** a pak vyberte **vytvořit Azure firewall zásady**.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Snímek obrazovky s postupem vytvoření zásady brány firewall ve Správci brány firewall" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. Na kartě **základy** zadejte požadované podrobnosti a vyberte **Další: nastavení DNS**. 

1. Na kartě **DNS** vyberte **Zakázat** a potom vyberte **Další: pravidla**.

1. Vyberte **přidat kolekci pravidel** , zadejte níže podrobnosti a vyberte **Přidat** a potom vyberte **Další: Analýza hrozeb**.

   -  Název
   -  Typ kolekce pravidel – DNAT
   -  Priorita
   -  Akce kolekce pravidel – povolení
   -  Název pravidla
   -  Typ zdroje – **IPaddress**
   -  Zdroj – * *\** _
   -  Protokol – _ *TCP**
   -  Cílový port – **80**
   -  Cílový typ – **IP adresa**
   -  Cíl – **Veřejná IP adresa**
   -  Přeložená adresa – **privátní IP adresa webového serveru řešení Azure VMware**
   -  Přeložený port – **port webového serveru řešení Azure VMware**

1. Ponechte výchozí hodnotu a pak vyberte **Další: rozbočovače**.

1. Vyberte **přidružit virtuální rozbočovač**.

1. V seznamu vyberte centrum a vyberte **Přidat**.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Snímek obrazovky zobrazující vybraná centra, která se budou převádět na zabezpečená virtuální centra" border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. Vyberte **Další: Značky**. 

1. Volitelné Vytvořte páry název a hodnota pro kategorizaci prostředků. 

1. Vyberte **Další: zkontrolovat + vytvořit** a pak vyberte **vytvořit**.

## <a name="limitations"></a>Omezení

Na SDDCs můžete mít veřejné IP adresy 100.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o používání veřejných IP adres pomocí [Azure Virtual WAN](../virtual-wan/virtual-wan-about.md).

