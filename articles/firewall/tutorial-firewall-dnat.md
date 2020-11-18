---
title: 'Kurz: filtrování příchozího internetového provozu pomocí Azure Firewall DNAT pomocí portálu'
description: V tomto kurzu zjistíte, jak nasadit a konfigurovat DNAT služby Azure Firewall pomocí webu Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 71eda40abd38b4885b8e88085e338667b608902f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655132"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Kurz: filtrování příchozího internetového provozu pomocí Azure Firewall DNAT pomocí Azure Portal

Ve službě Azure Firewall můžete nakonfigurovat překlad adres na základě cíle (DNAT) pro překlad a filtrování příchozího internetového provozu do vašich podsítí. Když nakonfigurujete DNAT, akce kolekce pravidel překladu adres (NAT) se nastaví na **DNAT**. Každé pravidlo v kolekci pravidel NAT pak lze použít k překladu veřejné IP adresy a portu brány firewall na privátní IP adresu a port. Pravidla DNAT implicitně přidávají odpovídající pravidlo sítě, které povoluje přeložený provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu. Další informace najdete v článku, který pojednává o [logice zpracování pravidel služby Azure Firewall](rule-processing.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvoření výchozí trasy
> * Konfigurace pravidla DNAT
> * Testování brány firewall

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.



## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na domovské stránce Azure Portal vyberte **skupiny prostředků** a pak vyberte **Přidat**.
3. Jako **Název skupiny prostředků** zadejte **RG-DNAT-Test**.
4. V části **Předplatné** vyberte své předplatné.
5. V části **Umístění skupiny prostředků** vyberte umístění. Všechny další prostředky, které vytvoříte, musí být ve stejném umístění.
6. Vyberte **Vytvořit**.

## <a name="set-up-the-network-environment"></a>Nastavení síťového prostředí

V tomto kurzu vytvoříte dvě partnerské virtuální sítě:

- **VN-Hub** – v této virtuální síti bude brána firewall.
- **VN-Spoke** – v této virtuální síti bude server úloh.

Nejprve vytvořte virtuální sítě a pak mezi nimi vytvořte partnerský vztah.

### <a name="create-the-hub-vnet"></a>Vytvoření virtuální sítě centra

1. Na domovské stránce Azure Portal vyberte **všechny služby**.
2. V části **síť** vyberte **virtuální sítě**.
3. Vyberte možnost **Přidat**.
4. Jako **Název** zadejte **VN-Hub**.
5. V části **Adresní prostor** zadejte **10.0.0.0/16**.
6. V části **Předplatné** vyberte své předplatné.
7. V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte **RG-DNAT-Test**.
8. V části **Umístění** vyberte dříve použité umístění.
9. V části **Podsíť** jako **Název** zadejte **AzureFirewallSubnet**.

     Brána firewall bude v této podsíti a název podsítě **musí** být AzureFirewallSubnet.
     > [!NOTE]
     > Velikost podsítě AzureFirewallSubnet je/26. Další informace o velikosti podsítě najdete v tématu [Azure firewall Nejčastější dotazy](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

10. Pro **Rozsah adres** zadejte **10.0.1.0/26**.
11. Použijte další výchozí nastavení a pak vyberte **vytvořit**.

### <a name="create-a-spoke-vnet"></a>Vytvoření virtuální sítě paprsku

1. Na domovské stránce Azure Portal vyberte **všechny služby**.
2. V části **síť** vyberte **virtuální sítě**.
3. Vyberte možnost **Přidat**.
4. Jako **Název** zadejte **VN-Spoke**.
5. Jako **Adresní prostor** zadejte **192.168.0.0/16**.
6. V části **Předplatné** vyberte své předplatné.
7. V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte **RG-DNAT-Test**.
8. V části **Umístění** vyberte dříve použité umístění.
9. V části **Podsíť** jako **Název** zadejte **SN-Workload**.

    V této podsíti bude server.
10. Jako **Rozsah adres** zadejte **192.168.1.0/24**.
11. Použijte další výchozí nastavení a pak vyberte **vytvořit**.

### <a name="peer-the-vnets"></a>Vytvoření partnerského vztahu virtuálních sítí

Teď mezi dvěma virtuálními sítěmi vytvořte partnerský vztah.

1. Vyberte virtuální síť **centra vn** .
2. V části **Nastavení** vyberte **partnerské vztahy**.
3. Vyberte možnost **Přidat**.
4. Zadejte **peer-HubSpoke** pro **název partnerského vztahu od VN-Hub do vn-paprsek**.
5. Jako virtuální síť vyberte **VN-Spoke**.
6. Zadejte **peer-SpokeHub** pro **název partnerského vztahu od VN-Spoke do služby vn – hub**.
7. Pro **Povolení přesměrovaného provozu od VN-Spoke do vn – vyberte rozbočovač** **povolený**.
8. Vyberte **OK**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač úloh a umístěte ho do podsítě **SN-Workload**.

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.
2. V části **Oblíbené** vyberte **Windows Server 2016 Datacenter**.

**Základy**

1. V části **Předplatné** vyberte své předplatné.
1. V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte **RG-DNAT-Test**.
1. Jako **název virtuálního počítače** zadejte **SRV-úlohy**.
1. V poli **oblast** vyberte stejné umístění, které jste použili dříve.
1. Zadejte uživatelské jméno a heslo.
1. Vyberte **Další: disky**.

**Disky**
1. Až skončíte, vyberte **Další: Sítě**.

**Sítě**

1. Pro **virtuální síť** vyberte **vn-paprsek**.
2. Jako **Podsíť** vyberte **SN-Workload**.
3. Pro **veřejnou IP adresu** vyberte **žádné**.
4. U **veřejných příchozích portů** vyberte **None (žádné**). 
2. Ponechte ostatní výchozí nastavení a vyberte **Další: Správa**.

**správy**

1. V případě **diagnostiky spouštění** vyberte **vypnuto**.
1. Vyberte **zkontrolovat + vytvořit**.

**Zkontrolovat a vytvořit**

Zkontrolujte souhrn a pak vyberte **vytvořit**. Dokončení může několik minut trvat.

Po dokončení nasazení si poznamenejte privátní IP adresu virtuálního počítače. Použijete ji později při konfiguraci brány firewall. Vyberte název virtuálního počítače a v části **Nastavení** vyberte **sítě** , abyste našli privátní IP adresu.

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

1. Na domovské stránce portálu vyberte **vytvořit prostředek**.
2. Vyberte **sítě** a po výběru **Doporučené** vyberte **Zobrazit vše**.
3. Vyberte **firewall** a pak vyberte **vytvořit**. 
4. Na stránce **Vytvoření brány firewall** nakonfigurujte bránu firewall podle následující tabulky:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Název     |FW-DNAT-test|
   |Předplatné     |\<your subscription\>|
   |Skupina prostředků     |**Použít existující:** RG-DNAT-Test |
   |Umístění     |Vyberte dříve použité umístění.|
   |Volba virtuální sítě     |**Použít existující:** VN-Hub|
   |Veřejná IP adresa     |**Vytvořte nový**. Veřejná IP adresa musí být typu Standardní SKU.|

5. Vyberte **Zkontrolovat a vytvořit**.
6. Zkontrolujte souhrn a pak vyberte **vytvořit** a vytvořte bránu firewall.

   Nasazení může několik minut trvat.
7. Po dokončení nasazení přejdete do skupiny prostředků **RG-DNAT-test** a vyberete bránu firewall **FW-DNAT-test** .
8. Poznamenejte si privátní IP adresu. Budete ji potřebovat později při vytváření výchozí trasy.

## <a name="create-a-default-route"></a>Vytvoření výchozí trasy

U podsítě **SN-Workload** nakonfigurujete výchozí trasu v odchozím směru, která půjde přes bránu firewall.

1. Na domovské stránce Azure Portal vyberte **všechny služby**.
2. V části **sítě** vyberte **směrovací tabulky**.
3. Vyberte možnost **Přidat**.
4. Jako **Název** zadejte **RT-FWroute**.
5. V části **Předplatné** vyberte své předplatné.
6. V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte **RG-DNAT-Test**.
7. V části **Umístění** vyberte dříve použité umístění.
8. Vyberte **Vytvořit**.
9. Vyberte **aktualizovat** a potom vyberte tabulku směrování **RT-FWroute** .
10. Vyberte **podsítě** a pak vyberte **přidružit**.
11. Vyberte **virtuální síť** a potom vyberte **vn-paprsek**.
12. Jako **Podsíť** vyberte **SN-Workload**.
13. Vyberte **OK**.
14. Vyberte **trasy** a pak vyberte **Přidat**.
15. Jako **Název trasy** zadejte **FW-DG**.
16. V části **Předpona IP adresy** zadejte **0.0.0.0/0**.
17. V části **Typ dalšího směrování** vyberte **Virtuální zařízení**.

    Brána Azure Firewall je ve skutečnosti spravovaná služba, ale v tomto případě bude virtuální zařízení fungovat.
18. V části **Adresa dalšího směrování** zadejte dříve poznamenanou privátní IP adresu brány firewall.
19. Vyberte **OK**.

## <a name="configure-a-nat-rule"></a>Konfigurace pravidla překladu adres (NAT)

1. Otevřete **RG-DNAT-test** a vyberte bránu firewall **FW-DNAT-test** . 
2. Na stránce **FW-DNAT-test** vyberte v části **Nastavení** možnost **pravidla**. 
3. Vyberte **přidat kolekci pravidel NAT**. 
4. Jako **Název** zadejte **RC-DNAT-01**. 
5. V části **Priorita** zadejte **200**. 
6. V části **Pravidla** jako **Název** zadejte **RL-01**.
7. V části **Protokol** vyberte **TCP**.
8. Do pole **Zdrojové adresy** zadejte *. 
9. Do pole **Cílové adresy** zadejte veřejnou IP adresu brány firewall. 
10. Do pole **Cílové porty** zadejte **3389**. 
11. Do pole **Přeložená adresa** zadejte privátní IP adresu virtuálního počítače Srv-Workload. 
12. Do pole **Přeložený port** zadejte **3389**. 
13. Vyberte možnost **Přidat**. 

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
> [Kurz: Monitorování protokolů brány Azure Firewall](./firewall-diagnostics.md)