---
title: 'Kurz: nasazení & konfigurace Azure Firewall pomocí Azure Portal'
description: V tomto kurzu se naučíte nasadit a konfigurovat bránu Azure Firewall pomocí webu Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 07/15/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: e73f11ec178c067941ee33e02f37c96605460ee0
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658583"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal

Řízení odchozího síťového přístupu je důležitou součástí celkového plánu zabezpečení sítě. Můžete například chtít omezit přístup k webům. Nebo můžete chtít omezit odchozí IP adresy a porty, které jsou k dispozici.

Jedním ze způsobů, jak můžete řídit odchozí síťový přístup z podsítě Azure, je použít Azure Firewall. Azure Firewall umožňuje nakonfigurovat:

* Pravidla aplikace, která definují plně kvalifikované názvy domén, ke kterým je možné získat přístup z podsítě.
* Pravidla sítě, která definují zdrojovou adresu, protokol, cílový port a cílovou adresu.

Síťový provoz podléhá nakonfigurovaným pravidlům brány firewall, když ho směrujete na bránu firewall jako na výchozí bránu podsítě.

V tomto kurzu vytvoříte zjednodušenou jednu virtuální síť se dvěma podsítěmi pro snadné nasazení.

V produkčních nasazeních se doporučuje [model hvězdicové a Paprskové](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) služby, kde brána firewall patří do vlastní virtuální sítě. Servery úloh jsou v virtuální sítě s partnerským vztahem ve stejné oblasti s jednou nebo více podsítěmi.

* **AzureFirewallSubnet** – v této podsíti bude brána firewall.
* **Workload-SN** – v této podsíti bude server úloh. Provoz této podsítě bude procházet bránou firewall.

![Kurz síťové infrastruktury](media/tutorial-firewall-deploy-portal/tutorial-network.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvoření výchozí trasy
> * Konfigurace pravidla použití pro povolení přístupu k www.google.com
> * Nakonfigurovat pravidlo sítě pro povolení přístupu k externím serverům DNS
> * Konfigurace pravidla překladu adres (NAT), které povolí vzdálenou plochu na testovacím serveru
> * Testování brány firewall

Pokud chcete, můžete k dokončení tohoto kurzu použít [Azure PowerShell](deploy-ps.md).

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="set-up-the-network"></a>Nastavit síť

Nejprve vytvořte skupinu prostředků obsahující prostředky potřebné k nasazení brány firewall. Pak vytvořte virtuální síť, podsítě a testovací server.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků obsahuje všechny prostředky pro tento kurz.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. V nabídce Azure Portal vyberte **skupiny prostředků** nebo vyhledejte a vyberte *skupiny prostředků* z libovolné stránky. Pak vyberte **Přidat**.
3. Jako **název skupiny prostředků** zadejte *test-FW-RG*.
4. V části **Předplatné** vyberte své předplatné.
5. V části **Umístění skupiny prostředků** vyberte umístění. Všechny ostatní prostředky, které vytvoříte, musí být ve stejném umístění.
6. Vyberte **Vytvořit**.

### <a name="create-a-vnet"></a>Vytvoření virtuální sítě

Tato virtuální síť bude obsahovat tři podsítě.

> [!NOTE]
> Velikost podsítě AzureFirewallSubnet je/26. Další informace o velikosti podsítě najdete v tématu [Azure firewall Nejčastější dotazy](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. V nabídce webu Azure Portal nebo na **domovské** stránce vyberte **Vytvořit prostředek**.
1. Vyberte **síť**  >  **virtuální síť**.
2. V části **Předplatné** vyberte své předplatné.
3. V případě **skupiny prostředků** vyberte **test-FW-RG**.
4. Jako **Název** zadejte **Test-FW-VN**.
5. V poli **oblast** vyberte stejné umístění, které jste použili dříve.
6. Vyberte **Další: IP adresy**.
7. V případě **adresního prostoru IPv4** zadejte **10.0.0.0/16**.
8. V části **podsíť** vyberte **výchozí**.
9. Jako **název podsítě** zadejte **AzureFirewallSubnet**. Brána firewall bude v této podsíti a název podsítě **musí** být AzureFirewallSubnet.
10. Pro **Rozsah adres** zadejte **10.0.1.0/26**.
11. Vyberte **Uložit**.

   Dále vytvořte podsíť pro server úloh.

1. Vyberte **Přidat podsíť**.
4. Jako **název podsítě** zadejte **úlohu-SN**.
5. Jako **Rozsah adres podsítě** zadejte **10.0.2.0/24**.
6. Vyberte možnost **Přidat**.
7. Vyberte **Zkontrolovat a vytvořit**.
8. Vyberte **Vytvořit**.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Nyní vytvořte virtuální počítač úlohy a umístěte ho do podsítě **zátěžového sériového zatížení** .

1. V nabídce webu Azure Portal nebo na **domovské** stránce vyberte **Vytvořit prostředek**.
2. Vyberte **COMPUTE** a potom vyberte **virtuální počítač**.
3. **Windows Server 2016 Datacenter** v seznamu Doporučené.
4. Zadejte pro virtuální počítač tyto hodnoty:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Skupina prostředků     |**Test-FW-RG**|
   |Název virtuálního počítače     |**SRV – práce**|
   |Oblast     |Stejné jako předchozí|
   |Image|Windows Server 2019 Datacenter|
   |Uživatelské jméno správce     |Zadejte uživatelské jméno.|
   |Heslo     |Zadejte heslo.|

4. V části **příchozí pravidla portů**, **veřejné příchozí porty** vyberte **žádné**.
6. Přijměte ostatní výchozí hodnoty a vyberte **Další: disky**.
7. Přijměte výchozí nastavení disku a vyberte **Další: sítě**.
8. Ujistěte se, že pro virtuální síť je vybraná možnost **test-FW-vn** a že podsíť je **zatížení s SN**.
9. V případě **veřejné IP adresy** vyberte **žádné**.
11. Přijměte ostatní výchozí hodnoty a vyberte **Další: Správa**.
12. Výběrem možnosti **vypnuto** zakážete diagnostiku spouštění. Přijměte ostatní výchozí hodnoty a vyberte **zkontrolovat + vytvořit**.
13. Zkontrolujte nastavení na stránce Souhrn a pak vyberte **vytvořit**.

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

Nasaďte do virtuální sítě bránu firewall.

1. V nabídce webu Azure Portal nebo na **domovské** stránce vyberte **Vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **firewall** a stiskněte klávesu **ENTER**.
3. Vyberte **firewall** a pak vyberte **vytvořit**.
4. Na stránce **Vytvoření brány firewall** nakonfigurujte bránu firewall podle následující tabulky:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Předplatné     |\<your subscription\>|
   |Skupina prostředků     |**Test-FW-RG** |
   |Název     |**Test-FW01**|
   |Umístění     |Vyberte dříve použité umístění.|
   |Volba virtuální sítě     |**Použít existující**: **test-FW-vn**|
   |Veřejná IP adresa     |**Přidat nový**<br>**Název**:  **FW-PIP**|

5. Vyberte **Zkontrolovat a vytvořit**.
6. Zkontrolujte souhrn a pak vyberte **vytvořit** a vytvořte bránu firewall.

   Nasazení může několik minut trvat.
7. Po dokončení nasazení přejdete do skupiny prostředků **test-FW-RG** a vyberete možnost **test-FW01** firewall.
8. Poznamenejte si privátní a veřejné IP adresy brány firewall. Tyto adresy budete používat později.

## <a name="create-a-default-route"></a>Vytvoření výchozí trasy

U podsítě **Workload-SN** nakonfigurujte výchozí trasu v odchozím směru, která půjde přes bránu firewall.

1. V nabídce Azure Portal vyberte **všechny služby** nebo vyhledejte a vyberte *všechny služby* z libovolné stránky.
2. V části **sítě** vyberte **směrovací tabulky**.
3. Vyberte možnost **Přidat**.
4. Jako **Název** zadejte **Firewall-route**.
5. V části **Předplatné** vyberte své předplatné.
6. V případě **skupiny prostředků** vyberte **test-FW-RG**.
7. V části **Umístění** vyberte dříve použité umístění.
8. Vyberte **Vytvořit**.
9. Vyberte **aktualizovat** a pak vyberte tabulku směrování **brány firewall** .
10. Vyberte **podsítě** a pak vyberte **přidružit**.
11. Vyberte **virtuální síť**  >  **test-FW-vn**.
12. V případě **podsítě** vyberte **úlohu – SN**. Ujistěte se, že jste pro tuto trasu vybrali pouze podsíť **zatížení sériového zatížení** sítě, jinak brána firewall nebude správně fungovat.

13. Vyberte **OK**.
14. Vyberte **trasy** a pak vyberte **Přidat**.
15. Jako **název trasy** zadejte **FW-DG**.
16. V části **Předpona IP adresy** zadejte **0.0.0.0/0**.
17. V části **Typ dalšího směrování** vyberte **Virtuální zařízení**.

    Brána Azure Firewall je ve skutečnosti spravovaná služba, ale v tomto případě bude virtuální zařízení fungovat.
18. V části **Adresa dalšího směrování** zadejte dříve poznamenanou privátní IP adresu brány firewall.
19. Vyberte **OK**.

## <a name="configure-an-application-rule"></a>Konfigurace pravidla aplikace

Toto je pravidlo aplikace, které umožňuje odchozí přístup `www.google.com` .

1. Otevřete **test-FW-RG** a vyberte firewall **test-FW01** .
2. Na stránce **test-FW01** v části **Nastavení** vyberte **pravidla**.
3. Vyberte kartu **kolekce pravidel aplikace** .
4. Vyberte **přidat kolekci pravidel aplikace**.
5. Jako **Název** zadejte **App-Coll01**.
6. V části **Priorita** zadejte **200**.
7. V části **Akce** vyberte **Povolit**.
8. V části **pravidla** zadejte **cílové plně kvalifikované názvy domény** pro **název** typ **Allow-Google**.
9. Jako **typ zdroje** vyberte **IP adresa**.
10. Jako **zdroj** zadejte **10.0.2.0/24**.
11. V části **Protokol:Port** zadejte **http, https**.
12. Pro **cílové plně kvalifikované názvy domén** zadejte **`www.google.com`**
13. Vyberte možnost **Přidat**.

Brána Azure Firewall obsahuje předdefinovanou kolekci pravidel pro infrastrukturu plně kvalifikovaných názvů domén, které jsou ve výchozím nastavení povolené. Tyto plně kvalifikované názvy domén jsou specifické pro tuto platformu a pro jiné účely je nelze použít. Další informace najdete v tématu [Plně kvalifikované názvy domén infrastruktury](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurace pravidla sítě

Toto pravidlo sítě povoluje odchozí přístup ke dvěma IP adresám na portu 53 (DNS).

1. Vyberte kartu **kolekce pravidel sítě** .
2. Vyberte **přidat kolekci pravidel sítě**.
3. Jako **název** zadejte **Net-Coll01**.
4. V části **Priorita** zadejte **200**.
5. V části **Akce** vyberte **Povolit**.
6. V části **pravidla**, **IP adresy** zadejte do pole **název** možnost **Allow-DNS**.
7. V části **Protokol** vyberte **UDP**.
9. Jako **typ zdroje** vyberte **IP adresa**.
1. Jako **zdroj** zadejte **10.0.2.0/24**.
2. Jako **cílový typ** vyberte **IP adresa**.
3. Pro **cílovou adresu** zadejte **209.244.0.3, 209.244.0.4**

   Jedná se o veřejné servery DNS provozované nástrojem CenturyLink.
1. V části **Cílové porty** zadejte **53**.
2. Vyberte možnost **Přidat**.

## <a name="configure-a-dnat-rule"></a>Konfigurace pravidla DNAT

Toto pravidlo umožňuje připojit vzdálenou plochu k Srv-Workmu virtuálnímu počítači přes bránu firewall.

1. Vyberte kartu **kolekce pravidel NAT** .
2. Vyberte **přidat kolekci pravidel NAT**.
3. Jako **název** zadejte **RDP**.
4. V části **Priorita** zadejte **200**.
5. V části **pravidla** zadejte do **pole název** **RDP-NAT**.
6. V části **Protokol** vyberte **TCP**.
7. Jako **typ zdroje** vyberte **IP adresa**.
8. Jako **zdroj** zadejte * *\** _.
9. Pro _ * cílová adresa * * zadejte veřejnou IP adresu brány firewall.
10. V případě **cílových portů** zadejte **3389**.
11. Pro **přeloženou adresu** zadejte privátní IP adresu pro **práci s SRV** .
12. Do pole **Přeložený port** zadejte **3389**.
13. Vyberte možnost **Přidat**.


### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Změna primární a sekundární adresy DNS u síťového rozhraní **Srv-Work**

Pro účely testování v tomto kurzu nakonfigurujte primární a sekundární adresy DNS serveru. Nejedná se o obecný požadavek Azure Firewall.

1. V nabídce Azure Portal vyberte **skupiny prostředků** nebo vyhledejte a vyberte *skupiny prostředků* z libovolné stránky. Vyberte skupinu prostředků **test-FW-RG** .
2. Vyberte síťové rozhraní pro virtuální počítač s **prací SRV** .
3. V části **Nastavení** vyberte **servery DNS**.
4. V části **servery DNS** vyberte **vlastní**.
5. Do textového pole **Přidat server DNS** zadejte **209.244.0.3** a do dalšího textového pole zadejte **209.244.0.4**.
6. Vyberte **Uložit**.
7. Restartujte virtuální počítač **Srv-Work**.

## <a name="test-the-firewall"></a>Testování brány firewall

Nyní otestujte bránu firewall a potvrďte, že funguje podle očekávání.

1. Připojte vzdálenou plochu k veřejné IP adrese firewall a přihlaste se k virtuálnímu počítači služby **SRV – pracovní** postup. 
3. Otevřete prohlížeč Internet Explorer a přejděte na adresu `https://www.google.com`.
4. **OK**  >  V okně výstrahy zabezpečení aplikace Internet Explorer vyberte OK **Zavřít** .

   Měla by se zobrazit Domovská stránka Google.

5. Přejděte na adresu `https://www.microsoft.com`.

   Brána firewall by vás měla zablokovat.

Takže teď ověříte, že pravidla brány firewall fungují:

* Můžete přejít na jediný povolený plně kvalifikovaný název domény, ale jinam už ne.
* Názvy DNS můžete přeložit pomocí nakonfigurovaného externího serveru DNS.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky brány firewall si můžete ponechat pro další kurz, nebo můžete odstraněním skupiny prostředků **Test-FW-RG** odstranit všechny prostředky související z bránou firewall, pokud už je nepotřebujete.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Monitorování protokolů brány Azure Firewall](./firewall-diagnostics.md)