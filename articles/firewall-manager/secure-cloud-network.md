---
title: 'Kurz: zabezpečení virtuálního centra pomocí správce Azure Firewall'
description: V tomto kurzu se naučíte zabezpečit virtuální rozbočovač pomocí nástroje Azure Firewall Manager pomocí Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: 14ec6fafbbadff2ecc37b229270c269f068a666f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104670456"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Kurz: zabezpečení virtuálního centra pomocí správce Azure Firewall

Pomocí správce Azure Firewall můžete vytvořit zabezpečená virtuální centra pro zabezpečení síťového provozu určeného na privátní IP adresy, do Azure PaaS a na Internet. Směrování provozu do brány firewall je automatizované, takže není nutné vytvářet trasy definované uživatelem (udr).

![zabezpečení cloudové sítě](media/secure-cloud-network/secure-cloud-network.png)

Správce brány firewall taky podporuje architekturu virtuální sítě rozbočovače. Porovnání typů architektury zabezpečeného virtuálního centra a centra virtuálních sítí najdete v tématu [co jsou možnosti architektury Azure firewall Manageru?](vhubs-and-vnets.md)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě paprsků
> * Vytvoření zabezpečeného virtuálního rozbočovače
> * Připojení virtuálních sítí hvězdicové a hvězdicové sítě
> * Směrování provozu do vašeho centra
> * Nasazení serverů
> * Vytvoření zásady brány firewall a zabezpečení centra
> * Testování brány firewall

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-hub-and-spoke-architecture"></a>Vytvoření architektury centra a paprsků

Nejdřív vytvořte virtuální sítě paprsků, kam můžete umístit své servery.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>Vytvořit dvě virtuální sítě a podsítě paprsků

Tyto dvě virtuální sítě budou mít v nich Server úloh a budou chráněny bránou firewall.

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Vyhledejte **virtuální síť** a vyberte **vytvořit**.
2. V části **Předplatné** vyberte své předplatné.
1. V poli **Skupina prostředků** vyberte **vytvořit novou** a jako název zadejte **FW-Manager-RG** a vyberte **OK**.
2. Jako **název** zadejte **paprsek-01**.
3. V **oblasti oblast** vyberte **(US) východní USA**.
4. Vyberte **Další: IP adresy**.
1. V části **Adresní prostor** zadejte **10.0.0.0/16**.
3. V části **název podsítě** vyberte **výchozí**.
4. Jako **název podsítě** zadejte  **úlohu-01-SN**.
5. Jako **Rozsah adres podsítě** zadejte **10.0.1.0/24**.
6. Vyberte **Uložit**.
1. Vyberte **Zkontrolovat a vytvořit**.
2. Vyberte **Vytvořit**.

Opakujte tento postup pro vytvoření další podobné virtuální sítě:

Název: **paprsek-02**<br>
Adresní prostor: **10.1.0.0/16**<br>
Název podsítě: úloha **-02-SN**<br>
Rozsah adres podsítě: **10.1.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>Vytvoření zabezpečeného virtuálního centra

Vytvořte zabezpečeného virtuálního centra pomocí Správce brány firewall.

1. Na domovské stránce Azure Portal vyberte **všechny služby**.
2. Do vyhledávacího pole zadejte **Správce brány** firewall a vyberte **Správce brány firewall**.
3. Na stránce **Správce brány firewall** vyberte **Zobrazit zabezpečená virtuální centra**.
4. **Správce brány firewall | Na stránce zabezpečená virtuální** centra vyberte **vytvořit nové zabezpečené virtuální rozbočovače**.
5. V případě **skupiny prostředků** vyberte **FW-Manager-RG**.
7. V **oblasti oblast** vyberte **východní USA**.
1. V poli **název zabezpečeného virtuálního centra** zadejte příkaz **hub-01**.
2. Jako **adresní prostor centra** zadejte **10.2.0.0/16**.
3. Pro nový název vWAN zadejte **vWAN-01**.
4. Zrušte zaškrtnutí políčka **Zahrnout bránu VPN pro povolení důvěryhodných partnerů zabezpečení** .
5. Vyberte **Další: Azure firewall**.
6. Přijměte výchozí nastavení **povoleno** **Azure firewall** a pak vyberte **Další: důvěryhodný bezpečnostní partner**.
7. Přijměte výchozí nastavení **zakázaný** **důvěryhodný partner zabezpečení** a vyberte **Další: zkontrolovat + vytvořit**.
8. Vyberte **Vytvořit**. 

   Nasazení bude trvat přibližně 30 minut.

Po dokončení nasazení můžete získat veřejnou IP adresu brány firewall.

1. Otevřete **Správce brány firewall**.
2. Vyberte **virtuální rozbočovače**.
3. Vyberte možnost **hub-01**.
7. Vyberte **Konfigurace veřejné IP adresy**.
8. Poznamenejte si veřejnou IP adresu, kterou použijete později.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>Připojení virtuálních sítí hvězdicové a hvězdicové sítě

Nyní můžete vytvořit partnerský vztah k virtuálním sítím rozbočovač a paprsek.

1. Vyberte skupinu prostředků **FW-Manager-RG** a pak vyberte **Vwan-01** Virtual WAN.
2. V části **připojení** vyberte **připojení k virtuální síti**.
3. Vyberte **Přidat připojení**.
4. Jako **název připojení** zadejte **hub-paprsek-01**.
5. V případě **rozbočovačů** vyberte možnost **hub-01**.
6. V případě **skupiny prostředků** vyberte **FW-Manager-RG**.
7. V případě **virtuální sítě** vyberte **paprskový-01**.
8. Vyberte **Vytvořit**.

Opakujte pro připojení k virtuální síti **paprske-02** : název připojení – **střed – paprskový-02**

## <a name="deploy-the-servers"></a>Nasazení serverů

1. V Azure Portal vyberte **vytvořit prostředek**.
2. V seznamu **oblíbených** vyberte **Windows Server 2016 Datacenter** .
3. Zadejte pro virtuální počítač tyto hodnoty:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Skupina prostředků     |**FW-Manager-RG**|
   |Název virtuálního počítače     |**SRV – zatížení-01**|
   |Oblast     |**VYLEPŠENÍ Východní USA)**|
   |Uživatelské jméno správce     |Zadejte uživatelské jméno.|
   |Heslo     |Zadejte heslo.|

4. V části **pravidla příchozího portu** pro **veřejné příchozí porty** vyberte **None (žádné**).
6. Přijměte ostatní výchozí hodnoty a vyberte **Další: disky**.
7. Přijměte výchozí nastavení disku a vyberte **Další: sítě**.
8. Vyberte pro virtuální síť **paprsk-01** a vyberte **úlohu-01-SN** pro podsíť.
9. V případě **veřejné IP adresy** vyberte **žádné**.
11. Přijměte ostatní výchozí hodnoty a vyberte **Další: Správa**.
12. Výběrem možnosti **Zakázat** diagnostiku spouštění zakážete. Přijměte ostatní výchozí hodnoty a vyberte **zkontrolovat + vytvořit**.
13. Zkontrolujte nastavení na stránce Souhrn a pak vyberte **vytvořit**.

Pomocí informací v následující tabulce můžete nakonfigurovat jiný virtuální počítač s názvem **SRV-úlohy-02**. Zbytek konfigurace je stejný jako virtuální počítač **SRV-zatížení-01** .

|Nastavení  |Hodnota  |
|---------|---------|
|Virtuální síť|**Paprskový-02**|
|Podsíť|**Zatížení 02 – SN**|

Po nasazení serverů vyberte prostředek serveru a v části **sítě** si poznamenejte privátní IP adresu každého serveru.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Vytvoření zásady brány firewall a zabezpečení centra

Zásady brány firewall definují kolekce pravidel pro směrování provozu na jednom nebo několika zabezpečených virtuálních rozbočovačích. Vytvoříte zásadu brány firewall a pak zabezpečíte své centrum.

1. V nástroji Správce brány firewall vyberte **zobrazit Azure firewall zásady**.
2. Vyberte **vytvořit Azure firewall zásady**.
1. V případě **skupiny prostředků** vyberte **FW-Manager-RG**.
1. V části **Podrobnosti o zásadách** zadejte **název** **zásady typu-01** a v **oblasti** vyberte **východní USA**.
1. Vyberte **Další: nastavení DNS**.
1. Vyberte **Další: Kontrola TLS (Preview)**.
1. Vyberte **Další: pravidla**.
1. Na kartě **pravidla** vyberte **přidat kolekci pravidel**.
1. Na stránce **přidat kolekci pravidel** zadejte pro **název** **App-RC-01** .
1. Jako **typ kolekce pravidel** vyberte **Application (aplikace**).
1. Jako **Priorita** zadejte **100**.
1. Zajistěte, aby **akce kolekce pravidel** byla **povolená**.
1. Jako **název** pravidla zadejte **Allow-MSFT**.
1. Jako **typ zdroje** vyberte **IP adresa**.
1. Jako **zdroj** zadejte **\*** .
1. V případě **protokolu** zadejte **http, https**.
1. Ujistěte se, že **cílový typ** je **plně kvalifikovaný název domény**.
1. Pro **cíl** zadejte **\* . Microsoft.com**.
1. Vyberte **Přidat**.

Přidejte pravidlo DNAT, abyste mohli připojit vzdálenou plochu k virtuálnímu počítači **SRV-úlohy-01** .

1. Vyberte možnost **přidat kolekci pravidel**.
1. Jako **název** zadejte **DNAT-RDP**.
1. Jako **typ kolekce pravidel** vyberte **DNAT**.
1. Jako **Priorita** zadejte **100**.
1. Jako **název** pravidla zadejte **Allow-RDP**.
1. Jako **typ zdroje** vyberte **IP adresa**.
1. Jako **zdroj** zadejte **\*** .
1. V části **Protokol** vyberte **TCP**.
1. V případě **cílových portů** zadejte **3389**.
1. Jako **typ cíle** vyberte **IP adresa**.
1. Do pole **cíl** zadejte veřejnou IP adresu brány firewall, kterou jste si poznamenali dříve.
1. Pro **přeloženou adresu** zadejte privátní IP adresu pro **SRV-úlohu-01** , kterou jste si poznamenali dříve.
1. Do pole **Přeložený port** zadejte **3389**.
1. Vyberte **Přidat**.

Přidejte pravidlo sítě, abyste mohli připojit vzdálenou plochu z **SRV-úlohy-01** do služby **SRV – úloha-02**.

1. Vyberte **přidat kolekci pravidel**.
2. Jako **název** zadejte **VNet-RDP**.
3. Jako **typ kolekce pravidel** vyberte **síť**.
4. Jako **Priorita** zadejte **100**.
1. V případě **akce kolekce pravidel** vyberte možnost **povoleno**.
1. Jako **název** pravidla zadejte **Allow-VNet**.
1. Jako **typ zdroje** vyberte **IP adresa**.
1. Jako **zdroj** zadejte **\*** .
1. V části **Protokol** vyberte **TCP**.
1. V případě **cílových portů** zadejte **3389**.
1. Jako **typ cíle** vyberte **IP adresa**.
1. Do pole **cíl** zadejte privátní IP adresu **SRV-úlohy-02** , kterou jste si poznamenali dříve.
1. Vyberte **Přidat**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

## <a name="associate-policy"></a>Přidružit zásady

Přidružte zásady brány firewall k centru.

1. V nástroji Správce brány firewall vyberte **Azure firewall zásady**.
1. Zaškrtněte políčko pro **zásadu-01**.
1. Vyberte **Spravovat přidružení/přidružit rozbočovače**.
1. Vyberte možnost **hub-01**.
1. Vyberte **Přidat**.

## <a name="route-traffic-to-your-hub"></a>Směrování provozu do vašeho centra

Teď je potřeba zajistit, aby byl síťový provoz směrován přes bránu firewall.

1. V nástroji Správce brány firewall vyberte **virtuální rozbočovače**.
2. Vyberte možnost **hub-01**.
3. V části **Nastavení** vyberte **Konfigurace zabezpečení**.
4. V části **internetový provoz** vyberte **Azure firewall**.
5. V části **privátní přenos** vyberte **Odeslat prostřednictvím Azure firewall**.
1. Vyberte **Uložit**.

   Aktualizace směrovacích tabulek trvá několik minut.
1. Ověřte, že se dvě připojení zobrazují Azure Firewall zabezpečení internetových i privátních přenosů.

## <a name="test-the-firewall"></a>Testování brány firewall

K otestování pravidel brány firewall připojíte vzdálenou plochu pomocí veřejné IP adresy brány firewall, která je NATed na **SRV-úlohy-01**. Odtud použijete prohlížeč k otestování pravidla aplikace a připojení vzdálené plochy ke službě **SRV – úloha-02** pro otestování pravidla sítě.

### <a name="test-the-application-rule"></a>Testování pravidla aplikace

Nyní otestujte pravidla brány firewall a potvrďte, že funguje podle očekávání.

1. Připojte vzdálenou plochu k veřejné IP adrese firewall a přihlaste se.

3. Otevřete prohlížeč Internet Explorer a přejděte na adresu `https://www.microsoft.com`.
4.   >  V okně výstrahy zabezpečení aplikace Internet Explorer vyberte OK **Zavřít** .

   Měla by se zobrazit Domovská stránka společnosti Microsoft.

5. Přejděte na adresu `https://www.google.com`.

   Brána firewall by vás měla zablokovat.

Takže teď ověříte, že pravidlo aplikace firewall funguje:

* Můžete přejít na jediný povolený plně kvalifikovaný název domény, ale jinam už ne.

### <a name="test-the-network-rule"></a>Testování síťového pravidla

Nyní otestujte pravidlo sítě.

- V části SRV-úlohy-01 otevřete vzdálenou plochu na privátní IP adresu služby SRV – úloha-02.

   Vzdálená plocha by se měla připojit ke službě SRV-úlohy-02.

Takže teď ověříte, že pravidlo sítě brány firewall funguje:
* Vzdálenou plochu můžete připojit k serveru umístěnému v jiné virtuální síti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení testování prostředků brány firewall odstraňte skupinu prostředků **FW-Manager-RG** , aby se odstranily všechny prostředky související s bránou firewall.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o důvěryhodných partnerech zabezpečení](trusted-security-partners.md)
