---
title: 'Kurz: Nasazení & konfigurace brány Azure Firewall pomocí portálu Azure'
description: V tomto kurzu se naučíte nasadit a konfigurovat bránu Azure Firewall pomocí webu Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/21/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 064fcf618914bca31ad9e7e60c76df8f599cd8bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239571"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal

Řízení odchozího síťového přístupu je důležitou součástí celkového plánu zabezpečení sítě. Můžete například omezit přístup k webovým serverům. Nebo můžete chtít omezit odchozí IP adresy a porty, které jsou přístupné.

Jedním ze způsobů, jak můžete řídit odchozí síťový přístup z podsítě Azure, je použít Azure Firewall. Azure Firewall umožňuje nakonfigurovat:

* Pravidla aplikace, která definují plně kvalifikované názvy domén, ke kterým je možné získat přístup z podsítě.
* Pravidla sítě, která definují zdrojovou adresu, protokol, cílový port a cílovou adresu.

Síťový provoz podléhá nakonfigurovaným pravidlům brány firewall, když ho směrujete na bránu firewall jako na výchozí bránu podsítě.

V tomto kurzu pro usnadnění nasazení vytvoříte jednu zjednodušenou virtuální síť se třemi podsítěmi. Pro nasazení v produkčním prostředí se doporučuje [model rozbočovače a paprsku.](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) Brána firewall je ve vlastní virtuální síti. Servery úloh jsou v partnerských virtuálních sítích ve stejné oblasti s jednou nebo více podsítěmi.

* **AzureFirewallSubnet** – v této podsíti bude brána firewall.
* **Workload-SN** – v této podsíti bude server úloh. Provoz této podsítě bude procházet bránou firewall.
* **Jump-SN** – v této podsíti bude „jump“ server. Jump server má veřejnou IP adresu, ke které se můžete připojit pomocí vzdálené plochy. Odtud se pak můžete připojit k serveru úloh (pomocí další vzdálené plochy).

![Kurz síťové infrastruktury](media/tutorial-firewall-rules-portal/Tutorial_network.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvoření výchozí trasy
> * Konfigurace pravidla aplikace pro povolení přístupu k www.google.com
> * Nakonfigurovat pravidlo sítě pro povolení přístupu k externím serverům DNS
> * Testování brány firewall

Pokud chcete, můžete k dokončení tohoto kurzu použít [Azure PowerShell](deploy-ps.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="set-up-the-network"></a>Nastavit síť

Nejprve vytvořte skupinu prostředků obsahující prostředky potřebné k nasazení brány firewall. Potom vytvořte virtuální síť, podsítě a testovací servery.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků obsahuje všechny prostředky pro tento kurz.

1. Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .
2. V nabídce Portál Azure vyberte **skupiny prostředků** nebo vyhledejte a vyberte *skupiny prostředků* na libovolné stránce. Pak vyberte **Přidat**.
3. Do **pole Název skupiny prostředků**zadejte *test-FW-RG*.
4. V části **Předplatné** vyberte své předplatné.
5. V části **Umístění skupiny prostředků** vyberte umístění. Všechny ostatní prostředky, které vytvoříte, musí být ve stejném umístění.
6. Vyberte **Vytvořit**.

### <a name="create-a-vnet"></a>Vytvoření virtuální sítě

Tato virtuální síť bude obsahovat tři podsítě.

> [!NOTE]
> Velikost podsítě AzureFirewallSubnet je /26. Další informace o velikosti podsítě najdete v [tématu Nejčastější dotazy k bráně Azure Firewall](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.
1. Vyberte **možnost Síťová** > **virtuální síť**.
1. Jako **Název** zadejte **Test-FW-VN**.
1. V části **Adresní prostor** zadejte **10.0.0.0/16**.
1. V části **Předplatné** vyberte své předplatné.
1. Ve **skupině Resource**vyberte možnost **Test-FW-RG**.
1. V části **Umístění** vyberte dříve použité umístění.
1. V části **Podsíť** jako **Název** zadejte **AzureFirewallSubnet**. Brána firewall bude v této podsíti a název podsítě **musí** být AzureFirewallSubnet.
1. Do **oblasti Adres**zadejte **10.0.1.0/26**.
1. Přijměte další výchozí nastavení a pak vyberte **Vytvořit**.

### <a name="create-additional-subnets"></a>Vytvoření dalších podsítí

V dalším kroku vytvoříme podsítě pro jump server a servery úloh.

1. V nabídce Portál Azure vyberte **skupiny prostředků** nebo vyhledejte a vyberte *skupiny prostředků* na libovolné stránce. Pak vyberte **Test-FW-RG**.
2. Vyberte virtuální síť **Test-FW-VN.**
3. Vyberte **podsítě** > **+podsíť**.
4. Jako **Název** zadejte **Workload-SN**.
5. V části **Rozsah adres** zadejte **10.0.2.0/24**.
6. Vyberte **OK**.

Vytvořte další podsíť s názvem **Jump-SN** a rozsahem adres **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Teď vytvoříte virtuální počítače pro jump server a server úloh a umístíte je do příslušných podsítí.

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.
2. Vyberte **Výpočty** a potom v seznamu Doporučené vyberte **Windows Server 2016 Datacenter**.
3. Zadejte pro virtuální počítač tyto hodnoty:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Skupina prostředků     |**Test-FW-RG**|
   |Název virtuálního počítače     |**Srv-Skok**|
   |Region (Oblast)     |Stejné jako předchozí|
   |Uživatelské jméno správce     |**azureuser**|
   |Heslo     |**Azure123456!** –|

4. V části **Pravidla příchozího portu**vyberte v části **Veřejné příchozí porty**možnost **Povolit vybrané porty**.
5. V **části Vybrat příchozí porty**vyberte možnost **RDP (3389).**

6. Přijměte další výchozí hodnoty a vyberte **možnost Další: Disky**.
7. Přijměte výchozí hodnoty disku a vyberte **možnost Další: Síť**.
8. Ujistěte se, že **test FW-VN** je vybrán pro virtuální síť a podsíť je **Jump-SN**.
9. V **případě veřejné IP adresy**přijměte výchozí název veřejné IP adresy (Srv-Jump-ip).
11. Přijměte další výchozí hodnoty a vyberte **Další: Správa**.
12. Chcete-li diagnostiku spouštění zakázat, vyberte **možnost Vypnuto.** Přijměte ostatní výchozí hodnoty a vyberte **Zkontrolovat + vytvořit**.
13. Zkontrolujte nastavení na stránce souhrnu a pak vyberte **Vytvořit**.

Informace v následující tabulce slouží ke konfiguraci jiného virtuálního počítače s názvem **Srv-Work**. Zbývající část konfigurace je stejná jako u virtuálního počítače Srv-Jump.

|Nastavení  |Hodnota  |
|---------|---------|
|Podsíť|**Workload-SN**|
|Veřejná IP adresa|**Žádné**|
|Veřejné příchozí porty|**Žádné**|

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

Nasaďte do virtuální sítě bránu firewall.

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **bránu firewall** a stiskněte **Enter**.
3. Vyberte **bránu firewall** a pak vyberte **Vytvořit**.
4. Na stránce **Vytvoření brány firewall** nakonfigurujte bránu firewall podle následující tabulky:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Předplatné     |\<Vaše předplatné\>|
   |Skupina prostředků     |**Test-FW-RG** |
   |Name (Název)     |**Test-FW01**|
   |Umístění     |Vyberte dříve použité umístění.|
   |Volba virtuální sítě     |**Použít existující**: **Test-FW-VN**|
   |Veřejná IP adresa     |**Přidat nový**. Veřejná IP adresa musí být typu Standardní SKU.|

5. Vyberte **Zkontrolovat a vytvořit**.
6. Zkontrolujte souhrn a pak vyberte **Vytvořit,** chcete-li vytvořit bránu firewall.

   Nasazení může několik minut trvat.
7. Po dokončení nasazení přejděte do skupiny prostředků **Test-FW-RG** a vyberte bránu firewall **Test-FW01.**
8. Poznamenejte si privátní IP adresu. Budete ji potřebovat později při vytváření výchozí trasy.

## <a name="create-a-default-route"></a>Vytvoření výchozí trasy

U podsítě **Workload-SN** nakonfigurujte výchozí trasu v odchozím směru, která půjde přes bránu firewall.

1. V nabídce Portál Azure vyberte **Všechny služby** nebo vyhledejte a vyberte *všechny služby* z libovolné stránky.
2. V části **Networking**vyberte **Možnost Směrovat tabulky**.
3. Vyberte **Přidat**.
4. Jako **Název** zadejte **Firewall-route**.
5. V části **Předplatné** vyberte své předplatné.
6. Ve **skupině Resource**vyberte možnost **Test-FW-RG**.
7. V části **Umístění** vyberte dříve použité umístění.
8. Vyberte **Vytvořit**.
9. Vyberte **Aktualizovat**a pak vyberte tabulku směrování **trasy brány firewall.**
10. Vyberte **Podsítě** a pak vyberte **Přidružit**.
11. Vyberte možnost**Test virtuální sítě-FW-VN** **Virtual network** > .
12. V **podsíti**vyberte **možnost Workload-SN**. Ujistěte se, že jste pro tuto trasu vybrali pouze podsíť **Workload-SN,** jinak brána firewall nebude fungovat správně.

13. Vyberte **OK**.
14. Vyberte **Trasy** a pak vyberte **Přidat**.
15. Do **pole Název trasy**zadejte příkaz **fw-dg**.
16. V části **Předpona IP adresy** zadejte **0.0.0.0/0**.
17. V části **Typ dalšího směrování** vyberte **Virtuální zařízení**.

    Brána Azure Firewall je ve skutečnosti spravovaná služba, ale v tomto případě bude virtuální zařízení fungovat.
18. V části **Adresa dalšího směrování** zadejte dříve poznamenanou privátní IP adresu brány firewall.
19. Vyberte **OK**.

## <a name="configure-an-application-rule"></a>Konfigurace pravidla aplikace

Toto je pravidlo aplikace, které umožňuje odchozí přístup k www.google.com.

1. Otevřete **test-FW-RG**a vyberte bránu firewall **Test-FW01.**
2. Na stránce **Test-FW01** vyberte v části **Nastavení** **položku Pravidla**.
3. Vyberte kartu **Kolekce pravidel aplikace.**
4. Vyberte **Přidat kolekci pravidel aplikace**.
5. Jako **Název** zadejte **App-Coll01**.
6. V části **Priorita** zadejte **200**.
7. V části **Akce** vyberte **Povolit**.
8. V **části Pravidla** **zadejte cílové názvy kvalifikovaných název**, v části **Název**napište **povolit Google**.
9. V **yberte Zdrojový typ**vyberte adresu **IP**.
10. Do **pole Zdroj**zadejte **10.0.2.0/24**.
11. V části **Protokol:Port** zadejte **http, https**.
12. Do **pole Target FQDNS**zadejte **www.google.com**
13. Vyberte **Přidat**.

Brána Azure Firewall obsahuje předdefinovanou kolekci pravidel pro infrastrukturu plně kvalifikovaných názvů domén, které jsou ve výchozím nastavení povolené. Tyto plně kvalifikované názvy domén jsou specifické pro tuto platformu a pro jiné účely je nelze použít. Další informace najdete v tématu [Plně kvalifikované názvy domén infrastruktury](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurace pravidla sítě

Toto pravidlo sítě povoluje odchozí přístup ke dvěma IP adresám na portu 53 (DNS).

1. Vyberte kartu **Kolekce síťových pravidel.**
2. Vyberte **Přidat kolekci síťových pravidel**.
3. Jako **název** zadejte **Net-Coll01**.
4. V části **Priorita** zadejte **200**.
5. V části **Akce** vyberte **Povolit**.
6. V části **Pravidla** **zadejte adresy IP**v **položce Název**příkaz **Allow-DNS**.
7. V části **Protokol** vyberte **UDP**.
9. V **yberte Zdrojový typ**vyberte adresu **IP**.
1. Do **pole Zdroj**zadejte **10.0.2.0/24**.
2. Pro **cílovou adresu**typ **209.244.0.3,209.244.0.4**

   Jedná se o veřejné DNS servery provozované CenturyLink.
1. V části **Cílové porty** zadejte **53**.
2. Vyberte **Přidat**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Změna primární a sekundární adresy DNS u síťového rozhraní **Srv-Work**

Pro účely testování v tomto kurzu nakonfigurujte primární a sekundární adresy DNS serveru. Nejedná se o obecný požadavek na bránu Azure Firewall.

1. V nabídce Portál Azure vyberte **skupiny prostředků** nebo vyhledejte a vyberte *skupiny prostředků* na libovolné stránce. Vyberte skupinu prostředků **Test-FW-RG.**
2. Vyberte síťové rozhraní pro virtuální počítač **Srv-Work.**
3. V části **Nastavení**vyberte **servery DNS**.
4. V části **DNS servery**vyberte **Vlastní**.
5. Do textového pole **Přidat server DNS** zadejte **209.244.0.3** a do dalšího textového pole zadejte **209.244.0.4**.
6. Vyberte **Uložit**.
7. Restartujte virtuální počítač **Srv-Work**.

## <a name="test-the-firewall"></a>Testování brány firewall

Nyní otestujte bránu firewall a potvrďte, že funguje podle očekávání.

1. Zkontrolujte na webu Azure Portal síťová nastavení virtuálního počítače **Srv-Work** a poznamenejte si privátní IP adresu.
2. Připojte vzdálenou plochu k virtuálnímu počítači **Srv-Jump** a přihlaste se. Odtud otevřete připojení ke vzdálené ploše k privární IP adrese **Srv-Work.**
3. Otevřete prohlížeč Internet Explorer a přejděte na adresu https://www.google.com.
4. V yberte **OK** > **Zavřít** v výstrahách zabezpečení aplikace Internet Explorer.

   Měli byste vidět domovskou stránku Google.

5. Přejděte na https://www.microsoft.com.

   Brána firewall by vás měla zablokovat.

Takže teď jste ověřili, že pravidla brány firewall fungují:

* Můžete přejít na jediný povolený plně kvalifikovaný název domény, ale jinam už ne.
* Názvy DNS můžete přeložit pomocí nakonfigurovaného externího serveru DNS.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky brány firewall si můžete ponechat pro další kurz, nebo můžete odstraněním skupiny prostředků **Test-FW-RG** odstranit všechny prostředky související z bránou firewall, pokud už je nepotřebujete.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
