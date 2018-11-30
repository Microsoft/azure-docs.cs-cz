---
title: 'Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal'
description: V tomto kurzu se naučíte nasadit a konfigurovat bránu Azure Firewall pomocí webu Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/15/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: be4cbc7e955e56853809378f98e9733ffe4a20c3
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633720"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal

Řízení odchozího síťového přístupu je důležitou součástí celkového plánu zabezpečení sítě. Můžete například chtít omezit přístup k webům nebo odchozí IP adresy a porty, ke kterým je možné přistupovat.

Jedním ze způsobů, jak můžete řídit odchozí síťový přístup z podsítě Azure, je použít Azure Firewall. Azure Firewall umožňuje nakonfigurovat:

* Pravidla aplikace, která definují plně kvalifikované názvy domén, ke kterým je možné získat přístup z podsítě.
* Pravidla sítě, která definují zdrojovou adresu, protokol, cílový port a cílovou adresu.

Síťový provoz podléhá nakonfigurovaným pravidlům brány firewall, když ho směrujete na bránu firewall jako na výchozí bránu podsítě.

V tomto kurzu pro usnadnění nasazení vytvoříte jednu zjednodušenou virtuální síť se třemi podsítěmi. Pro nasazení v produkčním prostředí doporučujeme [hvězdicový model](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), u kterého je brána firewall ve vlastní virtuální síti a servery úloh jsou v partnerských virtuálních sítích ve stejné oblasti s jednou nebo více podsítěmi.

- **AzureFirewallSubnet** – v této podsíti bude brána firewall.
- **Workload-SN** – v této podsíti bude server úloh. Provoz této podsítě bude procházet bránou firewall.
- **Jump-SN** – v této podsíti bude „jump“ server. Jump server má veřejnou IP adresu, ke které se můžete připojit pomocí vzdálené plochy. Odtud se pak můžete připojit k serveru úloh (pomocí další vzdálené plochy).

![Kurz síťové infrastruktury](media/tutorial-firewall-rules-portal/Tutorial_network.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvořit výchozí trasu
> * Konfigurace aplikace pro povolení přístupu k msn.com
> * Nakonfigurovat pravidlo sítě pro povolení přístupu k externím serverům DNS
> * Otestovat bránu firewall

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="set-up-the-network"></a>Nastavit síť

Nejprve vytvořte skupinu prostředků obsahující prostředky potřebné k nasazení brány firewall. Potom vytvořte virtuální síť, podsítě a testovací servery.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků obsahuje všechny prostředky pro tento kurz.

1. Přihlaste se k webu Azure Portal na adrese [http://portal.azure.com](http://portal.azure.com).
2. Na domovské stránce webu Azure Portal klikněte na **Skupiny prostředků** > **Přidat**.
3. Jako **Název skupiny prostředků** zadejte **Test-FW-RG**.
4. V části **Předplatné** vyberte své předplatné.
5. V části **Umístění skupiny prostředků** vyberte umístění. Všechny další prostředky, které vytvoříte, musí být ve stejném umístění.
6. Klikněte na možnost **Vytvořit**.

### <a name="create-a-vnet"></a>Vytvoření virtuální sítě

Tato virtuální síť bude obsahovat tři podsítě.

1. Na domovské stránce webu Azure Portal klikněte na **Všechny služby**.
2. V části **Sítě** klikněte na **Virtuální sítě**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Jako **Název** zadejte **Test-FW-VN**.
5. V části **Adresní prostor** zadejte **10.0.0.0/16**.
6. V části **Předplatné** vyberte své předplatné.
7. V části **Skupina prostředků** vyberte **Použít existující** > **Test-FW-RG**.
8. V části **Umístění** vyberte dříve použité umístění.
9. V části **Podsíť** jako **Název** zadejte **AzureFirewallSubnet**. Brána firewall bude v této podsíti a název podsítě **musí** být AzureFirewallSubnet.
10. V části **Rozsah adres** zadejte **10.0.1.0/24**.
11. Ostatní výchozí nastavení ponechte a potom klikněte na **Vytvořit**.

> [!NOTE]
> Minimální velikost podsítě AzureFirewallSubnet je /26.

### <a name="create-additional-subnets"></a>Vytvoření dalších podsítí

V dalším kroku vytvoříme podsítě pro jump server a servery úloh.

1. Na domovské stránce webu Azure Portal klikněte na **Skupiny prostředků** > **Test-FW-RG**.
2. Klikněte na virtuální síť **Test-FW-VN**.
3. Klikněte na **Podsítě** > **+ Podsíť**.
4. Jako **Název** zadejte **Workload-SN**.
5. V části **Rozsah adres** zadejte **10.0.2.0/24**.
6. Klikněte na **OK**.

Vytvořte další podsíť s názvem **Jump-SN** a rozsahem adres **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Teď vytvoříte virtuální počítače pro jump server a server úloh a umístíte je do příslušných podsítí.

1. Na webu Azure Portal klikněte na **Vytvořit prostředek**.
2. Klikněte na tlačítko **Compute** a pak vyberte **systému Windows Server 2016 Datacenter** v seznamu vybrané.
3. Zadejte pro virtuální počítač tyto hodnoty:

    - *Test-FW-RG* pro skupinu prostředků.
    - *SRV Jump* – název virtuálního počítače.
    - *azureuser* – uživatelské jméno správce.
    - *Azure123456!* – jako heslo.

4. V části **příchozí pravidla portů**, pro **veřejné příchozí porty**, klikněte na tlačítko **povolit vybrané porty**.
5. Pro **vyberte příchozí porty**vyberte **protokolu RDP (3389)**.

6. Přijměte ostatní výchozí hodnoty a klikněte na tlačítko **Další: disky**.
7. Přijměte výchozí nastavení disku a klikněte na tlačítko **Další: sítě**.
8. Ujistěte se, že **Test-FW VN** vybraná virtuální síť a podsíť je **Jump-SN**.
9. Pro **veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**.
10. Typ **Srv. Jump PIP** veřejnou IP adresu, název a klikněte na **OK**.
11. Přijměte ostatní výchozí hodnoty a klikněte na tlačítko **Další: Správa**.
12. Klikněte na tlačítko **vypnout** zakažte diagnostiku spouštění. Přijměte ostatní výchozí hodnoty a klikněte na tlačítko **revize + vytvořit**.
13. Zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **vytvořit**.

Celý postup zopakujte a vytvořte další virtuální počítač s názvem **Srv-Work**.

Pomocí informací v následující tabulce se nakonfigurovat virtuální počítač Srv práce. Zbývající část konfigurace je stejná jako u virtuálního počítače Srv-Jump.

|Nastavení  |Hodnota  |
|---------|---------|
|Podsíť|Workload-SN|
|Veřejná IP adresa|Žádný|
|Veřejné příchozí porty|Žádný|

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

Nasaďte do virtuální sítě bránu firewall.

1. Na domovské stránce webu Azure Portal klikněte na **Vytvořit prostředek**.
2. Klikněte na **Sítě** a vedle **Doporučené** klikněte na **Zobrazit vše**.
3. Klikněte na **Brána firewall** > **Vytvořit**. 
4. Na stránce **Vytvoření brány firewall** nakonfigurujte bránu firewall podle následující tabulky:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Název     |Test-FW01|
   |Předplatné     |\<Vaše předplatné\>|
   |Skupina prostředků     |**Použijte existující**: Test-FW-RG. |
   |Umístění     |Vyberte dříve použité umístění.|
   |Volba virtuální sítě     |**Použijte existující**: Test-FW-VN.|
   |Veřejná IP adresa     |**Vytvořte novou**. Veřejná IP adresa musí být typu Standardní SKU.|

5. Klikněte na **Zkontrolovat a vytvořit**.
6. Zkontrolujte souhrn a potom kliknutím na **Vytvořit** vytvořte bránu firewall.

   Nasazení může několik minut trvat.
7. Po dokončení nasazení přejděte do skupiny prostředků **Test-FW-RG** a klikněte na bránu firewall **Test-FW01**.
8. Poznamenejte si privátní IP adresu. Budete ji potřebovat později při vytváření výchozí trasy.

## <a name="create-a-default-route"></a>Vytvořit výchozí trasu

U podsítě **Workload-SN** nakonfigurujte výchozí trasu v odchozím směru, která půjde přes bránu firewall.

1. Na domovské stránce webu Azure Portal klikněte na **Všechny služby**.
2. V části **Sítě** klikněte na **Směrovací tabulky**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Jako **Název** zadejte **Firewall-route**.
5. V části **Předplatné** vyberte své předplatné.
6. V části **Skupina prostředků** vyberte **Použít existující** a potom vyberte **Test-FW-RG**.
7. V části **Umístění** vyberte dříve použité umístění.
8. Klikněte na možnost **Vytvořit**.
9. Klikněte na **Aktualizovat** a potom klikněte na směrovací tabulku **Firewall-route**.
10. Klikněte na **Podsítě** > **Přidružit**.
11. Klikněte na **Virtuální síť** > **Test-FW-VN**.
12. V části **Podsíť** klikněte na **Workload-SN**. Dbejte na to, abyste pro tuto trasu vybrali jenom podsíť **Workload-SN**, jinak nebude vaše brána firewall pracovat správně.

13. Klikněte na **OK**.
14. Klikněte na **Trasy** > **Přidat**.
15. Jako **Název trasy** zadejte **FW-DG**.
16. V části **Předpona IP adresy** zadejte **0.0.0.0/0**.
17. V části **Typ dalšího směrování** vyberte **Virtuální zařízení**.

    Brána Azure Firewall je ve skutečnosti spravovaná služba, ale v tomto případě bude virtuální zařízení fungovat.
18. V části **Adresa dalšího směrování** zadejte dříve poznamenanou privátní IP adresu brány firewall.
19. Klikněte na **OK**.

## <a name="configure-an-application-rule"></a>Konfigurace pravidla aplikace

Toto je pravidlo brány application, který umožňuje odchozí přístup k msn.com.

1. Otevřete skupinu prostředků **Test-FW-RG** a klikněte na bránu firewall **Test-FW01**.
2. Na stránce **Test-FW01** v části **Nastavení** klikněte na **Pravidla**.
3. Klikněte na tlačítko **kolekce pravidel aplikace** kartu.
4. Klikněte na **Přidat kolekci pravidel aplikace**.
5. Jako **Název** zadejte **App-Coll01**.
6. V části **Priorita** zadejte **200**.
7. V části **Akce** vyberte **Povolit**.
8. V části **pravidla**, **Target plně kvalifikované názvy domény**, pro **název**, typ **AllowGH**.
9. V části **Zdrojové adresy** zadejte **10.0.2.0/24**.
10. V části **Protokol:Port** zadejte **http, https**.
11. Pro **Target plně kvalifikované názvy domény**, typ **msn.com**
12. Klikněte na tlačítko **Add** (Přidat).

Brána Azure Firewall obsahuje předdefinovanou kolekci pravidel pro infrastrukturu plně kvalifikovaných názvů domén, které jsou ve výchozím nastavení povolené. Tyto plně kvalifikované názvy domén jsou specifické pro tuto platformu a pro jiné účely je nelze použít. Další informace najdete v tématu [Plně kvalifikované názvy domén infrastruktury](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurace pravidla sítě

Toto pravidlo sítě povoluje odchozí přístup ke dvěma IP adresám na portu 53 (DNS).

1. Klikněte na tlačítko **sítě kolekce pravidel** kartu.
2. Klikněte na **Přidat kolekci pravidel sítě**.
3. Jako **název** zadejte **Net-Coll01**.
4. V části **Priorita** zadejte **200**.
5. V části **Akce** vyberte **Povolit**.

6. V části **Pravidla** jako **Název** zadejte **AllowDNS**.
7. V části **Protokol** vyberte **UDP**.
8. V části **Zdrojové adresy** zadejte **10.0.2.0/24**.
9. V části Cílová adresa zadejte **209.244.0.3,209.244.0.4**
10. V části **Cílové porty** zadejte **53**.
11. Klikněte na tlačítko **Add** (Přidat).

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Změna primární a sekundární adresy DNS u síťového rozhraní **Srv-Work**

Pro účely testování v tomto kurzu nakonfigurujete primární a sekundární adresy DNS. Toto se obecně u brány Azure Firewall nevyžaduje.

1. Na webu Azure Portal otevřete skupinu prostředků **Test-FW-RG**.
2. Klikněte na síťové rozhraní virtuálního počítače **Srv-Work**.
3. V části **Nastavení** klikněte na **Servery DNS**.
4. V části **Servery DNS** klikněte na **Vlastní**.
5. Do textového pole **Přidat server DNS** zadejte **209.244.0.3** a do dalšího textového pole zadejte **209.244.0.4**.
6. Klikněte na **Uložit**. 
7. Restartujte virtuální počítač **Srv-Work**.

## <a name="test-the-firewall"></a>Otestovat bránu firewall

Teď bránu firewall otestujte a ověřte, že funguje podle očekávání.

1. Zkontrolujte na webu Azure Portal síťová nastavení virtuálního počítače **Srv-Work** a poznamenejte si privátní IP adresu.
2. Připojte k virtuálnímu počítači **Srv-Jump** vzdálenou plochu a z ní otevřete další připojení k vzdálené ploše pomocí privátní IP adresy virtuálního počítače **Srv-Work**.

3. Otevřete prohlížeč Internet Explorer a přejděte na adresu http://msn.com.
4. U výstrah zabezpečení klikněte na **OK** > **Zavřít**.

   Zobrazí se domovská stránka služby MSN.

5. Přejděte na http://www.msn.com.

   Brána firewall by vás měla zablokovat.

Nyní jste ověřili, že pravidla brány firewall fungují:

- Můžete přejít na jediný povolený plně kvalifikovaný název domény, ale jinam už ne.
- Názvy DNS můžete přeložit pomocí nakonfigurovaného externího serveru DNS.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky brány firewall si můžete ponechat pro další kurz, nebo můžete odstraněním skupiny prostředků **Test-FW-RG** odstranit všechny prostředky související z bránou firewall, pokud už je nepotřebujete.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
