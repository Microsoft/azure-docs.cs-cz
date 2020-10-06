---
title: Jak používat funkci veřejné IP adresy ve virtuální síti WAN
description: Tento článek vysvětluje, jak používat funkci veřejné IP adresy ve službě Azure Virtual WAN.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: ec8af45a98e82a7c1c657776c4fee2c3ef068dca
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744824"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-virtual-wan"></a>Jak používat funkci veřejné IP adresy v Azure Virtual WAN

Veřejná IP adresa je nová funkce při připojení řešení Azure VMware a zákazníci můžou pomocí dvou způsobů povolit veřejný přístup k Internetu. 

- Aplikace lze hostovat a publikovat v nástroji Application Gateway Load Balancer pro přenosy HTTP/HTTPS.
- Publikováno prostřednictvím funkcí veřejné IP adresy v Azure Virtual WAN.

V rámci nasazení privátního cloudu řešení Azure VMware se po povolení funkce veřejné IP adresy automaticky vytvoří a povolí požadované komponenty s automatizací:

-  Virtual WAN

-  Virtuální centrum sítě WAN s připojením ExpressRoute

-  Azure Firewall služby s veřejnou IP adresou

Tento článek podrobně popisuje, jak můžete využít funkci veřejné IP adresy ve virtuální síti WAN k vytváření prostředků, jako jsou webové servery, virtuální počítače a hostitele, přístupné prostřednictvím veřejné sítě.

## <a name="prerequisites"></a>Předpoklady

-   Prostředí řešení Azure VMware

-   WebServer běžící v prostředí řešení Azure VMware.

## <a name="reference-architecture"></a>Referenční architektura

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Diagram architektury veřejné IP adresy" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

Diagram architektury zobrazuje hostitelský server zákazníka hostovaný v prostředí řešení Azure VMware a nakonfigurovaný pomocí privátních IP adres RFC1918.  Tato webová služba je zpřístupněna pro Internet prostřednictvím funkce veřejné IP adresy virtuální sítě WAN.  Veřejná IP adresa je obvykle cílový překlad adres (NAT) přeložený v Azure Firewall. Pomocí pravidel DNAT se v zásadách brány firewall přeloží požadavky na veřejnou IP adresu na soukromou adresu (webserver) s portem.

Požadavky uživatelů přirazí na bránu firewall na veřejné IP adrese, která je zase přeložena na privátní IP adresu pomocí pravidel DNAT v Azure Firewall. Brána firewall zkontroluje tabulku překladu adres (NAT) a pokud požadavek odpovídá položce, přepošle provoz na přeloženou adresu a port v prostředí řešení Azure VMware.

Webový server obdrží požadavek a odpoví na požadované informace nebo stránku brány firewall a brána firewall přepošle informace uživateli na veřejnou IP adresu.

## <a name="test-case"></a>Testovací případ
V tomto scénáři je nutné publikovat na internetovém serveru službu IIS. K publikování webu na veřejné IP adrese použijte funkci veřejné IP adresy v řešení Azure VMware.  Nastavíme pravidla překladu adres (NAT) pro bránu firewall a získáte přístup k prostředku řešení Azure VMware (virtuální počítače s webserverem) s veřejnou IP adresou.

## <a name="deploy-virtual-wan"></a>Nasadit Virtual WAN.

1. Přihlaste se k Azure Portal a pak vyhledejte a vyberte **Řešení Azure VMware**.

1. Vyberte privátní cloud řešení Azure VMware.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Diagram architektury veřejné IP adresy" border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. V části **Spravovat**vyberte možnost **připojení**.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Diagram architektury veřejné IP adresy" border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. Vyberte kartu **Veřejná IP adresa** a pak vyberte **Konfigurovat**.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="Diagram architektury veřejné IP adresy" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

2.  Přijměte výchozí hodnoty nebo je změňte a pak vyberte **vytvořit**.

   -  Skupina prostředků virtuální sítě ve velké oblasti

   -  Název virtuální sítě ve velké oblasti

   -  Blok adres virtuálního rozbočovače

   -  Počet veřejných IP adres (1-100)

Dokončení nasazení všech součástí trvá přibližně jednu hodinu. Toto nasazení se musí nacházet jenom jednou, aby podporovalo všechny budoucí veřejné IP adresy pro toto prostředí řešení Azure VMware.  

>[!TIP]
>Stav můžete monitorovat z **oznamovací** oblasti. 

## <a name="view-and-add-public-ip-addresses"></a>Zobrazení a přidání veřejných IP adres

Pomocí následujících kroků můžeme kontrolovat a přidávat další veřejné IP adresy.

1. V Azure Portal vyhledejte a vyberte **firewall**.

1. Vyberte nasazenou bránu firewall a potom vyberte **navštívit Manager Azure firewall a nakonfigurujte a spravujte tuto bránu firewall**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Diagram architektury veřejné IP adresy" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Vyberte **Zabezpečená virtuální centra** a ze seznamu vyberte virtuální rozbočovač.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Diagram architektury veřejné IP adresy" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. Na stránce virtuální rozbočovač vyberte **Konfigurace veřejné IP**adresy a přidejte další veřejnou IP adresu a pak vyberte **Přidat**. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Diagram architektury veřejné IP adresy" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Zadejte požadovaný počet IP adres a vyberte **Přidat**.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Diagram architektury veřejné IP adresy" border="true":::


## <a name="create-firewall-policies"></a>Vytvoření zásad brány firewall

Po nasazení všech součástí je můžete zobrazit v přidané skupině prostředků. Dalším krokem je přidání zásady brány firewall.

1. V Azure Portal vyhledejte a vyberte **firewall**.

1. Vyberte nasazenou bránu firewall a potom vyberte **navštívit Manager Azure firewall a nakonfigurujte a spravujte tuto bránu firewall**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Diagram architektury veřejné IP adresy" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Vyberte **zásady Azure firewall** a pak vyberte **vytvořit Azure firewall zásady**.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Diagram architektury veřejné IP adresy" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. Na kartě **základy** zadejte požadované podrobnosti a vyberte **Další: nastavení DNS**. 

1. Na kartě **DNS** vyberte **Zakázat**a potom vyberte **Další: pravidla**.

1. Vyberte **přidat kolekci pravidel**, zadejte níže podrobnosti a vyberte **Přidat** a potom vyberte **Další: Analýza hrozeb**.

   -  Name
   -  Typ kolekce pravidel – DNAT
   -  Priorita
   -  Akce kolekce pravidel – povolení
   -  Název pravidla
   -  Typ zdroje – **IPaddress**
   -  Zdrojová **\***
   -  Protokol – **TCP**
   -  Cílový port – **80**
   -  Cílový typ – **IP adresa**
   -  Cíl – **Veřejná IP adresa**
   -  Přeložená adresa – **privátní IP adresa webového serveru řešení Azure VMware**
   -  Přeložený port – **port webového serveru řešení Azure VMware**

1. Ponechte výchozí hodnotu a pak vyberte **Další: rozbočovače**.

1. Vyberte **přidružit virtuální rozbočovač**.

   :::image type="content" source="media/public-ip-usage/associate-virtual-hubs-azure-firewall-policy.png" alt-text="Diagram architektury veřejné IP adresy" border="true" lightbox="media/public-ip-usage/associate-virtual-hubs-azure-firewall-policy.png":::

1. V seznamu vyberte centrum a vyberte **Přidat**.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Diagram architektury veřejné IP adresy" border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. Vyberte **Další: Značky**. 

1. Volitelné Vytvořte páry název/hodnota pro kategorizaci prostředků. 

1. Vyberte **Další: zkontrolovat + vytvořit** a pak vyberte **vytvořit**.

## <a name="limitations"></a>Omezení

Na SDDCs můžete mít veřejné IP adresy 100.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o používání veřejných IP adres pomocí [Azure Virtual WAN](../virtual-wan/virtual-wan-about.md).

