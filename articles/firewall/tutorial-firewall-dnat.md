---
title: 'Kurz: filtrování příchozího internetového provozu pomocí Azure Firewall DNAT pomocí portálu'
description: V tomto kurzu zjistíte, jak nasadit a konfigurovat DNAT služby Azure Firewall pomocí webu Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a1d3bdae1e870b094472a63d4b808d9df95c129d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741901"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Kurz: filtrování příchozího internetového provozu pomocí Azure Firewall DNAT pomocí Azure Portal

Ve službě Azure Firewall můžete nakonfigurovat překlad adres na základě cíle (DNAT) pro překlad a filtrování příchozího internetového provozu do vašich podsítí. Když nakonfigurujete DNAT, akce kolekce pravidel překladu adres (NAT) se nastaví na **DNAT**. Každé pravidlo v kolekci pravidel NAT se pak dá použít k překladu veřejné IP adresy a portu brány firewall na privátní IP adresu a port. Pravidla DNAT implicitně přidávají odpovídající pravidlo sítě, které povoluje přeložený provoz. Z bezpečnostních důvodů je doporučený postup přidat konkrétní internetový zdroj, který umožní DNAT přístup k síti a vyhnout se používání zástupných znaků. Další informace najdete v článku, který pojednává o [logice zpracování pravidel služby Azure Firewall](rule-processing.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvoření výchozí trasy
> * Konfigurace pravidla DNAT
> * Testování brány firewall

## <a name="prerequisites"></a>Požadavky

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).



## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na domovské stránce Azure Portal vyberte **skupiny prostředků** a pak vyberte **Přidat**.
4. V části **Předplatné** vyberte své předplatné.
1. Jako **Název skupiny prostředků** zadejte **RG-DNAT-Test**.
5. V **oblasti** vyberte oblast. Všechny ostatní prostředky, které vytvoříte, musí být ve stejné oblasti.
6. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

## <a name="set-up-the-network-environment"></a>Nastavení síťového prostředí

V tomto kurzu vytvoříte dvě partnerské virtuální sítě:

- **VN-Hub** – v této virtuální síti bude brána firewall.
- **VN-Spoke** – v této virtuální síti bude server úloh.

Nejprve vytvořte virtuální sítě a pak mezi nimi vytvořte partnerský vztah.

### <a name="create-the-hub-vnet"></a>Vytvoření virtuální sítě centra

1. Na domovské stránce Azure Portal vyberte **všechny služby**.
2. V části **síť** vyberte **virtuální sítě**.
3. Vyberte **Přidat**.
7. V případě **skupiny prostředků** vyberte **RG-DNAT-test**.
1. Jako **Název** zadejte **VN-Hub**.
1. V poli **oblast** vyberte stejnou oblast, kterou jste použili dříve.
1. Vyberte **Další: IP adresy**.
1. V případě **adresního prostoru IPv4** přijměte výchozí **10.0.0.0/16**.
1. V části **název podsítě** vyberte výchozí.
1. Upravte **název podsítě** a zadejte **AzureFirewallSubnet**.

     Brána firewall bude v této podsíti a název podsítě **musí** být AzureFirewallSubnet.
     > [!NOTE]
     > Velikost podsítě AzureFirewallSubnet je/26. Další informace o velikosti podsítě najdete v tématu [Azure firewall Nejčastější dotazy](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

10. Jako **Rozsah adres podsítě** zadejte **10.0.1.0/26**.
11. Vyberte **Uložit**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

### <a name="create-a-spoke-vnet"></a>Vytvoření virtuální sítě paprsku

1. Na domovské stránce Azure Portal vyberte **všechny služby**.
2. V části **síť** vyberte **virtuální sítě**.
3. Vyberte **Přidat**.
1. V případě **skupiny prostředků** vyberte **RG-DNAT-test**.
1. Jako **Název** zadejte **VN-Spoke**.
1. V poli **oblast** vyberte stejnou oblast, kterou jste použili dříve.
1. Vyberte **Další: IP adresy**.
1. V případě **adresního prostoru IPv4** upravte výchozí nastavení a zadejte **192.168.0.0/16**.
1. Vyberte **Přidat podsíť**.
1. Jako **název podsítě** zadejte **sn-zatížení**.
10. Jako **Rozsah adres podsítě** zadejte **192.168.1.0/24**.
11. Vyberte **Přidat**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

### <a name="peer-the-vnets"></a>Vytvoření partnerského vztahu virtuálních sítí

Teď mezi dvěma virtuálními sítěmi vytvořte partnerský vztah.

1. Vyberte virtuální síť **centra vn** .
2. V části **Nastavení** vyberte **partnerské vztahy**.
3. Vyberte **Přidat**.
4. V rámci **této virtuální sítě** zadejte pro **Název propojení partnerského vztahu** typ **peer-HubSpoke**.
5. V části **Vzdálená virtuální síť** zadejte pro **Název propojení partnerského vztahu** typ **peer-SpokeHub**. 
1. Jako virtuální síť vyberte **VN-Spoke**.
1. Přijměte všechny ostatní výchozí hodnoty a pak vyberte **Přidat**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač úloh a umístěte ho do podsítě **SN-Workload**.

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.
2. V části **Oblíbené** vyberte **Windows Server 2016 Datacenter**.

**Základy**

1. V části **Předplatné** vyberte své předplatné.
1. V případě **skupiny prostředků** vyberte **RG-DNAT-test**.
1. Jako **název virtuálního počítače** zadejte **SRV-úlohy**.
1. V poli **oblast** vyberte stejné umístění, které jste použili dříve.
1. Zadejte uživatelské jméno a heslo.
1. Vyberte **Další: disky**.

**Disky**
1. Až skončíte, vyberte **Další: Sítě**.

**Sítě**

1. Pro **virtuální síť** vyberte **vn-paprsek**.
2. Jako **Podsíť** vyberte **SN-Workload**.
3. V případě **veřejné IP adresy** vyberte **žádné**.
4. U **veřejných příchozích portů** vyberte **None (žádné**). 
2. Ponechte ostatní výchozí nastavení a vyberte **Další: Správa**.

**správy**

1. V případě **diagnostiky spouštění** vyberte **Zakázat**.
1. Vyberte **Zkontrolovat a vytvořit**.

**Zkontrolovat a vytvořit**

Zkontrolujte souhrn a pak vyberte **vytvořit**. Dokončení může několik minut trvat.

Po dokončení nasazení si poznamenejte privátní IP adresu virtuálního počítače. Použijete ji později při konfiguraci brány firewall. Vyberte název virtuálního počítače a v části **Nastavení** vyberte **sítě** , abyste našli privátní IP adresu.

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

1. Na domovské stránce portálu vyberte **vytvořit prostředek**.
1. Vyhledejte **bránu firewall** a potom vyberte možnost **Brána firewall**.
1. Vyberte **Vytvořit**. 
1. Na stránce **Vytvoření brány firewall** nakonfigurujte bránu firewall podle následující tabulky:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Předplatné     |\<your subscription\>|
   |Skupina prostředků     |Vybrat **RG-DNAT-test** |
   |Name     |**FW-DNAT-test**|
   |Oblast     |Vyberte dříve použité umístění.|
   |Správa brány firewall|**Ke správě této brány firewall použít pravidla brány firewall (Classic)**|
   |Volba virtuální sítě     |**Použít existující:** VN-Hub|
   |Veřejná IP adresa     |**Přidejte nový**, název: **FW-PIP**.|

5. Přijměte ostatní výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.
6. Zkontrolujte souhrn a pak vyberte **vytvořit** a vytvořte bránu firewall.

   Nasazení může několik minut trvat.
7. Po dokončení nasazení přejdete do skupiny prostředků **RG-DNAT-test** a vyberete bránu firewall **FW-DNAT-test** .
8. Poznamenejte si privátní a veřejné IP adresy brány firewall. Později je budete používat při vytváření výchozí trasy a pravidla překladu adres (NAT).

## <a name="create-a-default-route"></a>Vytvoření výchozí trasy

U podsítě **SN-Workload** nakonfigurujete výchozí trasu v odchozím směru, která půjde přes bránu firewall.

1. Na domovské stránce Azure Portal vyberte **všechny služby**.
2. V části **sítě** vyberte **směrovací tabulky**.
3. Vyberte **Přidat**.
5. V části **Předplatné** vyberte své předplatné.
1. V případě **skupiny prostředků** vyberte **RG-DNAT-test**.
1. V poli **oblast** vyberte stejnou oblast, kterou jste použili dříve.
1. Jako **Název** zadejte **RT-FWroute**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.
1. Vyberte **Přejít k prostředku**.
1. Vyberte **podsítě** a pak vyberte **přidružit**.
1. Pro **virtuální síť** vyberte **vn-paprsek**.
1. Jako **Podsíť** vyberte **SN-Workload**.
1. Vyberte **OK**.
1. Vyberte **trasy** a pak vyberte **Přidat**.
1. Jako **Název trasy** zadejte **FW-DG**.
1. V části **Předpona IP adresy** zadejte **0.0.0.0/0**.
1. V části **Typ dalšího směrování** vyberte **Virtuální zařízení**.

    Brána Azure Firewall je ve skutečnosti spravovaná služba, ale v tomto případě bude virtuální zařízení fungovat.
18. V části **Adresa dalšího směrování** zadejte dříve poznamenanou privátní IP adresu brány firewall.
19. Vyberte **OK**.

## <a name="configure-a-nat-rule"></a>Konfigurace pravidla překladu adres (NAT)

1. Otevřete skupinu prostředků **RG-DNAT-test** a vyberte bránu firewall **FW-DNAT-test** . 
2. Na stránce **FW-DNAT-test** vyberte v části **Nastavení** možnost **pravidla (Classic)**. 
3. Vyberte **přidat kolekci pravidel NAT**. 
4. Jako **Název** zadejte **RC-DNAT-01**. 
5. V části **Priorita** zadejte **200**. 
6. V části **Pravidla** jako **Název** zadejte **RL-01**.
7. V části **Protokol** vyberte **TCP**.
1. Jako **typ zdroje** vyberte **IP adresa**.
1. Jako **zdroj** zadejte *. 
1. Pro **cílové adresy** zadejte veřejnou IP adresu brány firewall. 
1. Do pole **Cílové porty** zadejte **3389**. 
1. Do pole **Přeložená adresa** zadejte privátní IP adresu virtuálního počítače Srv-Workload. 
1. Do pole **Přeložený port** zadejte **3389**. 
1. Vyberte **Přidat**. Dokončení může několik minut trvat.

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
