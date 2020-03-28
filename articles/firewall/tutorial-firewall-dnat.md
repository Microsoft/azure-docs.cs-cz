---
title: Filtrování příchozího internetového provozu pomocí DNAT brány Azure Firewall pomocí portálu
description: V tomto kurzu zjistíte, jak nasadit a konfigurovat DNAT služby Azure Firewall pomocí webu Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/02/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7220e48c6103352108bdb89e107bb862ee194040
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251491"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Kurz: Filtrování příchozího internetového provozu pomocí DNAT brány Azure Firewall pomocí portálu Azure

Překlad cílové síťové adresy Azure Firewall (DNAT) můžete nakonfigurovat tak, aby překládal a filtroval příchozí internetový provoz do podsítí. Při konfiguraci dnat je akce shromažďování pravidel NAT nastavena na **Dnat**. Každé pravidlo v kolekci pravidel NAT pak lze použít k překladu veřejné IP adresy a portu brány firewall na privátní IP adresu a port. Pravidla DNAT implicitně přidávají odpovídající pravidlo sítě, které povoluje přeložený provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu. Další informace najdete v článku, který pojednává o [logice zpracování pravidel služby Azure Firewall](rule-processing.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvoření výchozí trasy
> * Konfigurace pravidla DNAT
> * Testování brány firewall

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

V tomto kurzu vytvoříte dvě partnerské virtuální sítě:

- **VN-Hub** – v této virtuální síti bude brána firewall.
- **VN-Spoke** – v této virtuální síti bude server úloh.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .
2. Na domovské stránce portálu Azure vyberte **Skupiny prostředků**a pak vyberte **Přidat**.
3. Jako **Název skupiny prostředků** zadejte **RG-DNAT-Test**.
4. V části **Předplatné** vyberte své předplatné.
5. V části **Umístění skupiny prostředků** vyberte umístění. Všechny další prostředky, které vytvoříte, musí být ve stejném umístění.
6. Vyberte **Vytvořit**.

## <a name="set-up-the-network-environment"></a>Nastavení síťového prostředí

Nejprve vytvořte virtuální sítě a pak mezi nimi vytvořte partnerský vztah.

### <a name="create-the-hub-vnet"></a>Vytvoření virtuální sítě centra

1. Na domovské stránce portálu Azure vyberte **Všechny služby**.
2. V části **Networking**vyberte **Virtuální sítě**.
3. Vyberte **Přidat**.
4. Jako **Název** zadejte **VN-Hub**.
5. V části **Adresní prostor** zadejte **10.0.0.0/16**.
6. V části **Předplatné** vyberte své předplatné.
7. V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte **RG-DNAT-Test**.
8. V části **Umístění** vyberte dříve použité umístění.
9. V části **Podsíť** jako **Název** zadejte **AzureFirewallSubnet**.

     Brána firewall bude v této podsíti a název podsítě **musí** být AzureFirewallSubnet.
     > [!NOTE]
     > Velikost podsítě AzureFirewallSubnet je /26. Další informace o velikosti podsítě najdete v [tématu Nejčastější dotazy k bráně Azure Firewall](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

10. Do **oblasti Adres**zadejte **10.0.1.0/26**.
11. Použijte další výchozí nastavení a pak vyberte **Vytvořit**.

### <a name="create-a-spoke-vnet"></a>Vytvoření virtuální sítě paprsku

1. Na domovské stránce portálu Azure vyberte **Všechny služby**.
2. V části **Networking**vyberte **Virtuální sítě**.
3. Vyberte **Přidat**.
4. Jako **Název** zadejte **VN-Spoke**.
5. Jako **Adresní prostor** zadejte **192.168.0.0/16**.
6. V části **Předplatné** vyberte své předplatné.
7. V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte **RG-DNAT-Test**.
8. V části **Umístění** vyberte dříve použité umístění.
9. V části **Podsíť** jako **Název** zadejte **SN-Workload**.

    V této podsíti bude server.
10. Jako **Rozsah adres** zadejte **192.168.1.0/24**.
11. Použijte další výchozí nastavení a pak vyberte **Vytvořit**.

### <a name="peer-the-vnets"></a>Vytvoření partnerského vztahu virtuálních sítí

Teď mezi dvěma virtuálními sítěmi vytvořte partnerský vztah.

1. Vyberte virtuální síť **VN-Hub.**
2. V části **Nastavení**vyberte **Peerings**.
3. Vyberte **Přidat**.
4. Zadejte **Peer-HubSpoke** pro **název partnerského vztahu z VN-Hub do VN-Spoke**.
5. Jako virtuální síť vyberte **VN-Spoke**.
6. Zadejte **Peer-SpokeHub** pro **název partnerského vztahu z VN-Spoke do VN-Hub**.
7. V **povolit předávaný provoz z vn-spoke do vn-hub** uvolte **Povoleno**.
8. Vyberte **OK**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač úloh a umístěte ho do podsítě **SN-Workload**.

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.
2. V části **Oblíbené**vyberte **Windows Server 2016 Datacenter**.

**Základy**

1. V části **Předplatné** vyberte své předplatné.
1. V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte **RG-DNAT-Test**.
1. Do **pole Název virtuálního počítače**zadejte příkaz **Srv-Workload**.
1. V **části Oblast**vyberte stejné umístění, které jste použili dříve.
1. Zadejte uživatelské jméno a heslo.
1. Vyberte **další: Disky**.

**Disky**
1. Vyberte **další: Síť .**

**Síťové služby**

1. V **případě virtuální sítě**vyberte možnost **VN-Spoke**.
2. Jako **Podsíť** vyberte **SN-Workload**.
3. V **yberte Pro veřejnou IP adresu** možnost **Žádný**.
4. V **části Veřejné příchozí porty**vyberte možnost **Žádný**. 
2. Ponechte další výchozí nastavení a vyberte **Další : Správa**.

**správy**

1. V **části Diagnostika spouštění**vyberte **možnost Vypnuto**.
1. Vyberte **zkontrolovat + vytvořit**.

**Recenze + Vytvořit**

Zkontrolujte souhrn a pak vyberte **Vytvořit**. Dokončení tohoto procesu může několik minut trvat.

Po dokončení nasazení si poznamenejte privátní IP adresu virtuálního počítače. Použijete ji později při konfiguraci brány firewall. Vyberte název virtuálního počítače a v části **Nastavení**vyberte **Síť,** chcete-li najít privátní IP adresu.

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

1. Na domovské stránce portálu vyberte **Vytvořit zdroj**.
2. Vyberte **Síťové a**po **možnosti Doporučené**vyberte Zobrazit **vše**.
3. Vyberte **bránu firewall**a pak vyberte **Vytvořit**. 
4. Na stránce **Vytvoření brány firewall** nakonfigurujte bránu firewall podle následující tabulky:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Name (Název)     |FW-DNAT-test|
   |Předplatné     |\<Vaše předplatné\>|
   |Skupina prostředků     |**Použít existující:** RG-DNAT-Test |
   |Umístění     |Vyberte dříve použité umístění.|
   |Volba virtuální sítě     |**Použít existující:** VN-Hub|
   |Veřejná IP adresa     |**Vytvořit nový**. Veřejná IP adresa musí být typu Standardní SKU.|

5. Vyberte **Zkontrolovat a vytvořit**.
6. Zkontrolujte souhrn a pak vyberte **Vytvořit,** chcete-li vytvořit bránu firewall.

   Nasazení může několik minut trvat.
7. Po dokončení nasazení přejděte do skupiny prostředků **RG-DNAT-Test** a vyberte bránu firewall **pro testování FW-DNAT.**
8. Poznamenejte si privátní IP adresu. Budete ji potřebovat později při vytváření výchozí trasy.

## <a name="create-a-default-route"></a>Vytvoření výchozí trasy

U podsítě **SN-Workload** nakonfigurujete výchozí trasu v odchozím směru, která půjde přes bránu firewall.

1. Na domovské stránce portálu Azure vyberte **Všechny služby**.
2. V části **Networking**vyberte **Možnost Směrovat tabulky**.
3. Vyberte **Přidat**.
4. Jako **Název** zadejte **RT-FWroute**.
5. V části **Předplatné** vyberte své předplatné.
6. V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte **RG-DNAT-Test**.
7. V části **Umístění** vyberte dříve použité umístění.
8. Vyberte **Vytvořit**.
9. Vyberte **Aktualizovat**a pak vyberte tabulku trasy **RT-FWroute.**
10. Vyberte **podsítě**a pak vyberte **Přidružit**.
11. Vyberte **Virtuální síť**a pak vyberte **VN-Spoke**.
12. Jako **Podsíť** vyberte **SN-Workload**.
13. Vyberte **OK**.
14. Vyberte **Trasy**a pak vyberte **Přidat**.
15. Jako **Název trasy** zadejte **FW-DG**.
16. V části **Předpona IP adresy** zadejte **0.0.0.0/0**.
17. V části **Typ dalšího směrování** vyberte **Virtuální zařízení**.

    Brána Azure Firewall je ve skutečnosti spravovaná služba, ale v tomto případě bude virtuální zařízení fungovat.
18. V části **Adresa dalšího směrování** zadejte dříve poznamenanou privátní IP adresu brány firewall.
19. Vyberte **OK**.

## <a name="configure-a-nat-rule"></a>Konfigurace pravidla navádění sítě

1. Otevřete **RG-DNAT-Test**a vyberte bránu firewall **pro test FW-DNAT.** 
2. Na stránce **testu FW-DNAT** vyberte v části **Nastavení** **pravidla**. 
3. Vyberte **Přidat kolekci pravidel NAT**. 
4. Jako **Název** zadejte **RC-DNAT-01**. 
5. V části **Priorita** zadejte **200**. 
6. V části **Pravidla** jako **Název** zadejte **RL-01**.
7. V části **Protokol** vyberte **TCP**.
8. Do pole **Zdrojové adresy** zadejte *. 
9. Do pole **Cílové adresy** zadejte veřejnou IP adresu brány firewall. 
10. Do pole **Cílové porty** zadejte **3389**. 
11. Do pole **Přeložená adresa** zadejte privátní IP adresu virtuálního počítače Srv-Workload. 
12. Do pole **Přeložený port** zadejte **3389**. 
13. Vyberte **Přidat**. 

## <a name="test-the-firewall"></a>Testování brány firewall

1. Připojte k veřejné IP adrese brány firewall vzdálenou plochu. Měli byste se připojit k virtuálnímu počítači **Srv-Workload**.
2. Zavřete vzdálenou plochu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky brány firewall si můžete ponechat pro další kurz, nebo můžete odstraněním skupiny prostředků **RG-DNAT-Test** odstranit všechny prostředky související z bránou firewall, pokud už je nepotřebujete.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvoření výchozí trasy
> * Konfigurace pravidla DNAT
> * Testování brány firewall

Dál můžete pokračovat monitorováním protokolů brány Azure Firewall.

> [!div class="nextstepaction"]
> [Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
