---
title: 'Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal'
description: V tomto kurzu se naučíte nasadit a konfigurovat bránu Azure Firewall pomocí webu Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 4/9/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: cd7797ae3b79fb874bafc89437943b084020d800
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194216"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal

Řízení odchozího síťového přístupu je důležitou součástí celkového plánu zabezpečení sítě. Můžete například omezit přístup k webovým stránkám. Nebo můžete chtít omezit odchozí IP adresy a porty, které mohou být přístupné.

Jedním ze způsobů, jak můžete řídit odchozí síťový přístup z podsítě Azure, je použít Azure Firewall. Azure Firewall umožňuje nakonfigurovat:

* Pravidla aplikace, která definují plně kvalifikované názvy domén, ke kterým je možné získat přístup z podsítě.
* Pravidla sítě, která definují zdrojovou adresu, protokol, cílový port a cílovou adresu.

Síťový provoz podléhá nakonfigurovaným pravidlům brány firewall, když ho směrujete na bránu firewall jako na výchozí bránu podsítě.

V tomto kurzu pro usnadnění nasazení vytvoříte jednu zjednodušenou virtuální síť se třemi podsítěmi. Pro nasazení v produkčním prostředí [model střed a paprsek](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) se doporučuje, pokud brána firewall je ve své vlastní virtuální sítě. Servery úlohy jsou v partnerských virtuálních sítích ve stejné oblasti pomocí jedné nebo několika podsítí.

* **AzureFirewallSubnet** – v této podsíti bude brána firewall.
* **Workload-SN** – v této podsíti bude server úloh. Provoz této podsítě bude procházet bránou firewall.
* **Jump-SN** – v této podsíti bude „jump“ server. Jump server má veřejnou IP adresu, ke které se můžete připojit pomocí vzdálené plochy. Odtud se pak můžete připojit k serveru úloh (pomocí další vzdálené plochy).

![Kurz síťové infrastruktury](media/tutorial-firewall-rules-portal/Tutorial_network.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvořit výchozí trasu
> * Konfigurace pravidla aplikace pro povolení přístupu k www.google.com
> * Nakonfigurovat pravidlo sítě pro povolení přístupu k externím serverům DNS
> * Otestovat bránu firewall

Pokud chcete, můžete k dokončení tohoto kurzu použít [Azure PowerShell](deploy-ps.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="set-up-the-network"></a>Nastavit síť

Nejprve vytvořte skupinu prostředků obsahující prostředky potřebné k nasazení brány firewall. Potom vytvořte virtuální síť, podsítě a testovací servery.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků obsahuje všechny prostředky pro tento kurz.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na Azure domovské stránky portálu, vyberte **skupiny prostředků** > **přidat**.
3. Jako **Název skupiny prostředků** zadejte **Test-FW-RG**.
4. V části **Předplatné** vyberte své předplatné.
5. V části **Umístění skupiny prostředků** vyberte umístění. Všechny další prostředky, které vytvoříte, musí být ve stejném umístění.
6. Vyberte **Vytvořit**.

### <a name="create-a-vnet"></a>Vytvoření virtuální sítě

Tato virtuální síť bude obsahovat tři podsítě.

1. Azure portal domovské stránce vyberte **vytvořit prostředek**.
2. V části **sítě**vyberte **virtuální síť**.
4. Jako **Název** zadejte **Test-FW-VN**.
5. V části **Adresní prostor** zadejte **10.0.0.0/16**.
6. V části **Předplatné** vyberte své předplatné.
7. Pro **skupiny prostředků**vyberte **Test-FW-RG**.
8. V části **Umístění** vyberte dříve použité umístění.
9. V části **Podsíť** jako **Název** zadejte **AzureFirewallSubnet**. Brána firewall bude v této podsíti a název podsítě **musí** být AzureFirewallSubnet.
10. V části **Rozsah adres** zadejte **10.0.1.0/24**.
11. Přijměte ostatní výchozí nastavení a pak vyberte **vytvořit**.

> [!NOTE]
> Minimální velikost podsítě AzureFirewallSubnet je /26.

### <a name="create-additional-subnets"></a>Vytvoření dalších podsítí

V dalším kroku vytvoříme podsítě pro jump server a servery úloh.

1. Na Azure domovské stránky portálu, vyberte **skupiny prostředků** > **Test-FW-RG**.
2. Vyberte **Test-FW VN** virtuální sítě.
3. Vyberte **podsítě** > **+ podsíť**.
4. Jako **Název** zadejte **Workload-SN**.
5. V části **Rozsah adres** zadejte **10.0.2.0/24**.
6. Vyberte **OK**.

Vytvořte další podsíť s názvem **Jump-SN** a rozsahem adres **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Teď vytvoříte virtuální počítače pro jump server a server úloh a umístíte je do příslušných podsítí.

1. Na webu Azure portal, vyberte **vytvořit prostředek**.
2. Vyberte **Výpočty** a potom v seznamu Doporučené vyberte **Windows Server 2016 Datacenter**.
3. Zadejte pro virtuální počítač tyto hodnoty:

   |Nastavení  |Value  |
   |---------|---------|
   |Skupina prostředků     |**Test-FW-RG**|
   |Název virtuálního počítače     |**SRV Jump**|
   |Oblast     |Stejné jako předchozí|
   |Uživatelské jméno správce     |**azureuser**|
   |Heslo     |**Azure123456!** –|

4. V části **příchozí pravidla portů**, pro **veřejné příchozí porty**vyberte **povolit vybrané porty**.
5. Pro **vyberte příchozí porty**vyberte **protokolu RDP (3389)**.

6. Přijměte ostatní výchozí hodnoty a vyberte **Další: Disky**.
7. Přijměte výchozí hodnoty disk a vyberte **Další: Sítě**.
8. Ujistěte se, že **Test-FW VN** vybraná virtuální síť a podsíť je **Jump-SN**.
9. Pro **veřejnou IP adresu**, přijměte výchozí novou veřejnou ip adresu název (Srv-Jump-ip).
11. Přijměte ostatní výchozí hodnoty a vyberte **Další: Správa**.
12. Vyberte **vypnout** zakažte diagnostiku spouštění. Přijměte ostatní výchozí hodnoty a vyberte **revize + vytvořit**.
13. Zkontrolujte nastavení na stránce Souhrn a pak vyberte **vytvořit**.

Použijte informace v následující tabulce ke konfiguraci další virtuální počítač s názvem **Srv pracovní**. Zbývající část konfigurace je stejná jako u virtuálního počítače Srv-Jump.

|Nastavení  |Value  |
|---------|---------|
|Podsíť|**Workload-SN**|
|Veřejná IP adresa|**Žádné**|
|Veřejné příchozí porty|**Žádné**|

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

Nasaďte do virtuální sítě bránu firewall.

1. Z domovské stránky portálu vyberte **vytvořit prostředek**.
2. Typ **brány firewall** do vyhledávacího pole a stiskněte klávesu **Enter**.
3. Vyberte **brány Firewall** a pak vyberte **vytvořit**.
4. Na stránce **Vytvoření brány firewall** nakonfigurujte bránu firewall podle následující tabulky:

   |Nastavení  |Value  |
   |---------|---------|
   |Předplatné     |\<Vaše předplatné\>|
   |Skupina prostředků     |**Test-FW-RG** |
   |Name     |**Test-FW01**|
   |Location     |Vyberte dříve použité umístění.|
   |Volba virtuální sítě     |**Použít existující**: **Test-FW-VN**|
   |Veřejná IP adresa     |**Vytvořte novou**. Veřejná IP adresa musí být typu Standardní SKU.|

5. Vyberte **Zkontrolovat a vytvořit**.
6. Zkontrolujte souhrn a pak vyberte **vytvořit** vytvořit bránu firewall.

   Nasazení může několik minut trvat.
7. Po dokončení nasazení přejděte k **Test-FW-RG** prostředku, skupiny a vyberte **testovací FW01** brány firewall.
8. Poznamenejte si privátní IP adresu. Budete ji potřebovat později při vytváření výchozí trasy.

## <a name="create-a-default-route"></a>Vytvořit výchozí trasu

U podsítě **Workload-SN** nakonfigurujte výchozí trasu v odchozím směru, která půjde přes bránu firewall.

1. Azure portal domovské stránce vyberte **všechny služby**.
2. V části **sítě**vyberte **směrovací tabulky**.
3. Vyberte **Přidat**.
4. Jako **Název** zadejte **Firewall-route**.
5. V části **Předplatné** vyberte své předplatné.
6. Pro **skupiny prostředků**vyberte **Test-FW-RG**.
7. V části **Umístění** vyberte dříve použité umístění.
8. Vyberte **Vytvořit**.
9. Vyberte **aktualizovat**a pak vyberte **trasy brány Firewall** směrovací tabulky.
10. Vyberte **podsítě** a pak vyberte **přidružit**.
11. Vyberte **virtuální síť** > **Test-FW VN**.
12. Pro **podsítě**vyberte **úloh-SN**. Ujistěte se, že jste vybrali pouze **úloh-SN** podsítě pro tuto trasu, jinak brána firewall nebude fungovat správně.

13. Vyberte **OK**.
14. Vyberte **trasy** a pak vyberte **přidat**.
15. Pro **trasy název**, typ **fw distribuční skupině**.
16. V části **Předpona IP adresy** zadejte **0.0.0.0/0**.
17. V části **Typ dalšího směrování** vyberte **Virtuální zařízení**.

    Brána Azure Firewall je ve skutečnosti spravovaná služba, ale v tomto případě bude virtuální zařízení fungovat.
18. V části **Adresa dalšího směrování** zadejte dříve poznamenanou privátní IP adresu brány firewall.
19. Vyberte **OK**.

## <a name="configure-an-application-rule"></a>Konfigurace pravidla aplikace

Toto je pravidlo brány application, který umožňuje odchozí přístup k www.google.com.

1. Otevřít **Test-FW-RG**a vyberte **testovací FW01** brány firewall.
2. Na **testovací FW01** stránce v části **nastavení**vyberte **pravidla**.
3. Vyberte **kolekce pravidel aplikace** kartu.
4. Vyberte **přidat kolekci pravidel aplikace**.
5. Jako **Název** zadejte **App-Coll01**.
6. V části **Priorita** zadejte **200**.
7. V části **Akce** vyberte **Povolit**.
8. V části **pravidla**, **Target plně kvalifikované názvy domény**, pro **název**, typ **povolit Google**.
9. V části **Zdrojové adresy** zadejte **10.0.2.0/24**.
10. V části **Protokol:Port** zadejte **http, https**.
11. Pro **Target plně kvalifikované názvy domény**, typ **www.google.com**
12. Vyberte **Přidat**.

Brána Azure Firewall obsahuje předdefinovanou kolekci pravidel pro infrastrukturu plně kvalifikovaných názvů domén, které jsou ve výchozím nastavení povolené. Tyto plně kvalifikované názvy domén jsou specifické pro tuto platformu a pro jiné účely je nelze použít. Další informace najdete v tématu [Plně kvalifikované názvy domén infrastruktury](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurace pravidla sítě

Toto pravidlo sítě povoluje odchozí přístup ke dvěma IP adresám na portu 53 (DNS).

1. Vyberte **sítě kolekce pravidel** kartu.
2. Vyberte **přidat kolekci pravidel sítě**.
3. Jako **název** zadejte **Net-Coll01**.
4. V části **Priorita** zadejte **200**.
5. V části **Akce** vyberte **Povolit**.

6. V části **pravidla**, pro **název**, typ **povolit DNS**.
7. V části **Protokol** vyberte **UDP**.
8. V části **Zdrojové adresy** zadejte **10.0.2.0/24**.
9. V části Cílová adresa zadejte **209.244.0.3,209.244.0.4**
10. V části **Cílové porty** zadejte **53**.
11. Vyberte **Přidat**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Změna primární a sekundární adresy DNS u síťového rozhraní **Srv-Work**

Pro účely testování v tomto kurzu, nakonfigurujte DNS adresy primárního a sekundárního serveru. Není to povinné obecné Brána Firewall služby Azure.

1. Na webu Azure Portal otevřete skupinu prostředků **Test-FW-RG**.
2. Vyberte síťové rozhraní pro **Srv pracovní** virtuálního počítače.
3. V části **nastavení**vyberte **servery DNS**.
4. V části **servery DNS**vyberte **vlastní**.
5. Do textového pole **Přidat server DNS** zadejte **209.244.0.3** a do dalšího textového pole zadejte **209.244.0.4**.
6. Vyberte **Uložit**.
7. Restartujte virtuální počítač **Srv-Work**.

## <a name="test-the-firewall"></a>Otestovat bránu firewall

Teď otestujte bránu firewall, aby ověřili, že funguje podle očekávání.

1. Zkontrolujte na webu Azure Portal síťová nastavení virtuálního počítače **Srv-Work** a poznamenejte si privátní IP adresu.
2. Připojení k vzdálené plochy **Srv Jump** virtuálního počítače a přihlášení. Odtud otevření připojení ke vzdálené ploše **Srv pracovní** privátní IP adresu.

3. Otevřete prohlížeč Internet Explorer a přejděte na adresu http://www.google.com.
4. Vyberte **OK** > **Zavřít** na výstrahy zabezpečení aplikace Internet Explorer.

   Zobrazí se domovská stránka služby Google.

5. Přejděte na http://www.microsoft.com.

   Brána firewall by vás měla zablokovat.

Teď jste ověřili funkčnost pravidla brány firewall:

* Můžete přejít na jediný povolený plně kvalifikovaný název domény, ale jinam už ne.
* Názvy DNS můžete přeložit pomocí nakonfigurovaného externího serveru DNS.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky brány firewall si můžete ponechat pro další kurz, nebo můžete odstraněním skupiny prostředků **Test-FW-RG** odstranit všechny prostředky související z bránou firewall, pokud už je nepotřebujete.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Monitorujte protokoly brány Firewall na Azure](./tutorial-diagnostics.md)
