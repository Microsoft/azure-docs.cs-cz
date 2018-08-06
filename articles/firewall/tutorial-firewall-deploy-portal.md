---
title: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal
description: V tomto kurzu se naučíte nasadit a konfigurovat bránu Azure Firewall pomocí webu Azure Portal.
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: tutorial
ms.date: 7/11/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: be11ea2195705b344638b93ea2657481897d6ef7
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358942"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal

Brána Azure Firewall má k řízení odchozího přístupu dva typy pravidel:

- **Pravidla aplikace**

   Umožňují nakonfigurovat plně kvalifikované názvy domény, ke kterým je možné získat přístup z podsítě. Například byste mohli ze své podsítě umožnit přístup ke stránce *github.com*.
- **Pravidla sítě**

   Umožňují nakonfigurovat pravidla obsahující zdrojovou adresu, protokol, cílový port a cílovou adresu. Například můžete vytvořit pravidlo, které povolí provoz portu 53 (DNS) na IP adresy vašeho serveru DNS z podsítě.

Síťový provoz podléhá nakonfigurovaným pravidlům brány firewall, když ho směrujete na bránu firewall jako na výchozí bránu podsítě.

Aplikace a síťová pravidla se ukládají do *kolekcí pravidel*. Kolekce pravidel je seznam pravidel, které sdílí stejnou akci a prioritu.  Kolekce pravidel sítě je seznam pravidel sítě a kolekce pravidel aplikace je seznam pravidel aplikace.

Kolekce pravidel sítě se zpracovávají vždy před kolekcemi pravidel aplikace. Všechna pravidla jsou ukončující, takže když se najde shoda v kolekci pravidel sítě, následující kolekce pravidel aplikace se v dané relaci už nezpracují.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvořit výchozí trasu
> * Konfigurovat pravidla aplikace
> * Konfigurovat pravidla sítě
> * Otestovat bránu firewall



Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Příklady v článcích o bráně Azure Firewall předpokládají, že už máte veřejnou verzi Preview brány Azure Firewall zapnutou. Další informace najdete v tématu o [povolení veřejné verze Preview brány Azure Firewall](public-preview.md).

Pro účely tohoto kurzu vytvořte jednu virtuální síť se třemi podsítěmi:
- **FW-SN** – v této podsíti bude brána firewall.
- **Workload-SN** – v této podsíti bude server úloh. Provoz této podsítě bude procházet bránou firewall.
- **Jump-SN** – v této podsíti bude „jump“ server. Jump server má veřejnou IP adresu, ke které se můžete připojit pomocí vzdálené plochy. Odtud se pak můžete připojit k serveru úloh (pomocí další vzdálené plochy).

![Kurz síťové infrastruktury](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Tento kurz používá kvůli snadnému nasazení zjednodušenou konfiguraci sítě. Pro nasazení v produkčním prostředí doporučujeme [hvězdicový model](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), u kterého je brána firewall ve vlastní virtuální síti a servery úloh jsou v partnerských virtuálních sítích ve stejné oblasti s jednou nebo více podsítěmi.



## <a name="set-up-the-network-environment"></a>Nastavení síťového prostředí
Nejprve vytvořte skupinu prostředků obsahující prostředky potřebné k nasazení brány firewall. Potom vytvořte virtuální síť, podsítě a testovací servery.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
1. Přihlaste se k webu Azure Portal na adrese [http://portal.azure.com](http://portal.azure.com).
1. Na domovské stránce webu Azure Portal klikněte na **Skupiny prostředků** a potom klikněte na **Přidat**.
2. Jako **Název skupiny prostředků** zadejte **Test-FW-RG**.
3. V části **Předplatné** vyberte své předplatné.
4. V části **Umístění skupiny prostředků** vyberte umístění. Všechny další prostředky, které vytvoříte, musí být ve stejném umístění.
5. Klikněte na možnost **Vytvořit**.


### <a name="create-a-vnet"></a>Vytvoření virtuální sítě
1. Na domovské stránce webu Azure Portal klikněte na **Všechny služby**.
2. V části **Sítě** klikněte na **Virtuální sítě**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Jako **Název** zadejte **Test-FW-VN**.
5. V části **Adresní prostor** zadejte **10.0.0.0/16**.
7. V části **Předplatné** vyberte své předplatné.
8. V části **Skupina prostředků** vyberte **Použít existující** a potom vyberte **Test-FW-RG**.
9. V části **Umístění** vyberte dříve použité umístění.
10. V části **Podsíť** jako **Název** zadejte **AzureFirewallSubnet**.

    Brána firewall bude v této podsíti a název podsítě **musí** být AzureFirewallSubnet.
11. V části **Rozsah adres** zadejte **10.0.1.0/24**.
12. Ostatní výchozí nastavení ponechte a potom klikněte na **Vytvořit**.

> [!NOTE]
> Minimální velikost podsítě AzureFirewallSubnet je /25.

### <a name="create-additional-subnets"></a>Vytvoření dalších podsítí

V dalším kroku vytvoříme podsítě pro jump server a servery úloh.

1. Na domovské stránce webu Azure Portal klikněte na **Skupiny prostředků** a potom klikněte na **Test-FW-RG**.
2. Klikněte na virtuální síť **Test-FW-VN**.
3. Klikněte na **Podsítě** a pak na **+ Podsíť**.
4. Jako **Název** zadejte **Workload-SN**.
5. V části **Rozsah adres** zadejte **10.0.2.0/24**.
6. Klikněte na **OK**.

Vytvořte další podsíť s názvem **Jump-SN** a rozsahem adres **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Teď vytvoříte virtuální počítače pro jump server a server úloh a umístíte je do příslušných podsítí.

1. Na domovské stránce webu Azure Portal klikněte na **Všechny služby**.
2. V části **Compute** klikněte na **Virtuální počítače**.
3. Klikněte na **Přidat**, potom na **Windows Server**, dále na **Windows Server 2016 Datacenter** a nakonec klikněte na **Vytvořit**.

**Základy**

1. Jako **Název** zadejte **Srv-Jump**.
5. Zadejte uživatelské jméno a heslo.
6. V části **Předplatné** vyberte své předplatné.
7. V části **Skupina prostředků** klikněte na **Použít existující** a potom vyberte **Test-FW-RG**.
8. V části **Umístění** vyberte dříve použité umístění.
9. Klikněte na **OK**.

**Velikost**

1. Zvolte vhodnou velikost pro testovací virtuální počítač s Windows Serverem. Například **B2ms** (8 GB paměti RAM, 16 GB úložiště).
2. Klikněte na **Vybrat**.

**Nastavení**

1. V části **Sítě** jako **Virtuální síť** vyberte **Test-FW-VN**.
2. V části **Podsíť** vyberte **Jump-SN**.
3. V části **Vyberte veřejné příchozí porty** vyberte **RDP (3389)**. 

    Přístup k vaší veřejné IP adrese budete chtít omezit, ale potřebujete otevřít port 3389, abyste se mohli k jump serveru připojit pomocí vzdálené plochy. 
2. Ostatní výchozí nastavení ponechte a klikněte na **OK**.

**Souhrn**

Zkontrolujte souhrn a potom klikněte na **Vytvořit**. Dokončení tohoto procesu může několik minut trvat.

Celý postup zopakujte a vytvořte další virtuální počítač s názvem **Srv-Work**.

Informace z následující tabulky použijte ke konfiguraci **Nastavení** virtuálního počítače Srv-Work. Zbývající část konfigurace je stejná jako u virtuálního počítače Srv-Jump.


|Nastavení  |Hodnota  |
|---------|---------|
|Podsíť|Workload-SN|
|Veřejná IP adresa|Žádný|
|Vyberte veřejné příchozí porty|Žádné veřejné příchozí porty|


## <a name="deploy-the-firewall"></a>Nasazení brány firewall

1. Na domovské stránce webu Azure Portal klikněte na **Vytvořit prostředek**.
2. Klikněte na **Sítě** a vedle **Doporučené** klikněte na **Zobrazit vše**.
3. Klikněte na bránu **Firewall** a potom vyberte **Vytvořit**. 
4. Na stránce **Vytvoření brány firewall** nakonfigurujte bránu firewall podle následující tabulky:
   
   |Nastavení  |Hodnota  |
   |---------|---------|
   |Název     |Test-FW01|
   |Předplatné     |\<Vaše předplatné\>|
   |Skupina prostředků     |**Použijte existující**: Test-FW-RG. |
   |Umístění     |Vyberte dříve použité umístění.|
   |Volba virtuální sítě     |**Použijte existující**: Test-FW-VN.|
   |Veřejná IP adresa     |Vytvořit nový|

2. Klikněte na **Zkontrolovat a vytvořit**.
3. Zkontrolujte souhrn a potom kliknutím na **Vytvořit** vytvořte bránu firewall.

   Nasazení může několik minut trvat.
4. Po dokončení nasazení přejděte do skupiny prostředků **Test-FW-RG** a klikněte na bránu firewall **Test-FW01**.
6. Poznamenejte si privátní IP adresu. Budete ji potřebovat později při vytváření výchozí trasy.

> [!NOTE]
> Veřejná IP adresa musí být typu Standardní SKU.

[//]: # (Nezapomeňte si poznamenat privátní IP adresu brány firewall.)

## <a name="create-a-default-route"></a>Vytvoření výchozí trasy

U podsítě **Workload-SN** nakonfigurujete výchozí trasu v odchozím směru, která půjde přes bránu firewall.

1. Na domovské stránce webu Azure Portal klikněte na **Všechny služby**.
2. V části **Sítě** klikněte na **Směrovací tabulky**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Jako **Název** zadejte **Firewall-route**.
5. V části **Předplatné** vyberte své předplatné.
6. V části **Skupina prostředků** vyberte **Použít existující** a potom vyberte **Test-FW-RG**.
7. V části **Umístění** vyberte dříve použité umístění.
8. Klikněte na možnost **Vytvořit**.
9. Klikněte na **Aktualizovat** a potom klikněte na směrovací tabulku **Firewall-route**.
10. Klikněte na **Podsítě** a pak na **Přidružit**.
11. Klikněte na **Virtuální síť** a potom vyberte **Test-FW-VN**.
12. V části **Podsíť** klikněte na **Workload-SN**.
13. Klikněte na **OK**.
14. Klikněte na **Trasy** a pak na **Přidat**.
15. Jako **Název trasy** zadejte **FW-DG**.
16. V části **Předpona IP adresy** zadejte **0.0.0.0/0**.
17. V části **Typ dalšího směrování** vyberte **Virtuální zařízení**.

    Brána Azure Firewall je ve skutečnosti spravovaná služba, ale v tomto případě bude virtuální zařízení fungovat.
1. V části **Adresa dalšího směrování** zadejte dříve poznamenanou privátní IP adresu brány firewall.
2. Klikněte na **OK**.


## <a name="configure-application-rules"></a>Konfigurace pravidel aplikace


1. Otevřete skupinu prostředků **Test-FW-RG** a klikněte na bránu firewall **Test-FW01**.
1. Na stránce **Test-FW01** v části **Nastavení** klikněte na **Pravidla**.
2. Klikněte na **Přidat kolekci pravidel aplikace**.
3. Jako **Název** zadejte **App-Coll01**.
1. V části **Priorita** zadejte **200**.
2. V části **Akce** vyberte **Povolit**.

6. V části **Pravidla** jako **Název** zadejte **AllowGH**.
7. V části **Zdrojové adresy** zadejte **10.0.2.0/24**.
8. V části **Protokol:Port** zadejte **http, https**. 
9. V části **Cílové plně kvalifikované názvy domén** zadejte **github.com**.
10. Klikněte na tlačítko **Add** (Přidat).

> [!NOTE]
> Brána Azure Firewall obsahuje předdefinovanou kolekci pravidel pro infrastrukturu plně kvalifikovaných názvů domén, které jsou ve výchozím nastavení povolené. Tyto plně kvalifikované názvy domén jsou specifické pro tuto platformu a pro jiné účely je nelze použít. Infrastruktura povolených plně kvalifikovaných názvů domén zahrnuje:
>- Výpočetní přístup k úložišti PIR (Platform Image Repository)
>- Přístup k úložišti se stavem spravovaných disků
>- Program Diagnostika
>
> Tuto předdefinovanou kolekci pravidel infrastruktury můžete přepsat vytvořením kolekce pravidel aplikace *deny all*, která se zpracuje jako poslední. Vždy se zpracuje před kolekcí pravidel infrastruktury. Vše mimo kolekci pravidel infrastruktury je ve výchozím nastavení zakázané.

## <a name="configure-network-rules"></a>Konfigurace pravidel sítě

1. Klikněte na **Přidat kolekci pravidel sítě**.
2. Jako **název** zadejte **Net-Coll01**.
3. V části **Priorita** zadejte **200**.
4. V části **Akce** vyberte **Povolit**.

6. V části **Pravidla** jako **Název** zadejte **AllowDNS**.
8. V části **Protokol** vyberte **UDP**.
9. V části **Zdrojové adresy** zadejte **10.0.2.0/24**.
10. V části Cílová adresa zadejte **209.244.0.3,209.244.0.4**
11. V části **Cílové porty** zadejte **53**.
12. Klikněte na tlačítko **Add** (Přidat).

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Změna primární a sekundární adresy DNS u síťového rozhraní **Srv-Work**

Pro účely testování v tomto kurzu nakonfigurujete primární a sekundární adresy DNS. Toto se obecně u brány Azure Firewall nevyžaduje. 

1. Na webu Azure Portal otevřete skupinu prostředků **Test-FW-RG**.
2. Klikněte na síťové rozhraní virtuálního počítače **Srv-Work**.
3. V části **Nastavení** klikněte na **Servery DNS**.
4. V části **Servery DNS** klikněte na **Vlastní**.
5. Do textového pole **Přidat server DNS** zadejte **209.244.0.3** a do dalšího textového pole zadejte **209.244.0.4**.
6. Klikněte na **Uložit**. 
7. Restartujte virtuální počítač **Srv-Work**.


## <a name="test-the-firewall"></a>Testování brány firewall

1. Zkontrolujte na webu Azure Portal síťová nastavení virtuálního počítače **Srv-Work** a poznamenejte si privátní IP adresu.
2. Připojte k virtuálnímu počítači **Srv-Jump** vzdálenou plochu a z ní otevřete další připojení k vzdálené ploše pomocí privátní IP adresy virtuálního počítače **Srv-Work**.

5. Otevřete prohlížeč Internet Explorer a přejděte na adresu http://github.com.
6. U výstrah zabezpečení klikněte na **OK** a **Zavřít**.

   Měla by se zobrazit domovská stránka GitHubu.

7. Přejděte na http://www.msn.com.

   Brána firewall by vás měla zablokovat.

Nyní jste ověřili, že pravidla brány firewall fungují:

- Můžete přejít na jediný povolený plně kvalifikovaný název domény, ale jinam už ne.
- Názvy DNS můžete přeložit pomocí nakonfigurovaného externího serveru DNS.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete žádně prostředky související s bránou firewall, odstraňte skupinu prostředků **Test-FW-RG**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvořit bránu firewall
> * Vytvořit výchozí trasu
> * Nakonfigurovat pravidla aplikace a síťové brány firewall
> * Otestovat bránu firewall

Dál můžete pokračovat monitorováním protokolů brány Azure Firewall.

> [!div class="nextstepaction"]
> [Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
