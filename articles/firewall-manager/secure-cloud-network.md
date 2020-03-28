---
title: 'Kurz: Zabezpečení virtuální sítě WAN pomocí náhledu Správce azure firewallu'
description: V tomto kurzu se dozvíte, jak zabezpečit virtuální WAN pomocí Správce Azure Firewall pomocí portálu Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 3dc94a8be265682fbe2128f2e5870dfdf5850a2d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77443053"
---
# <a name="tutorial-secure-your-virtual-wan-using-azure-firewall-manager-preview"></a>Kurz: Zabezpečení virtuální sítě WAN pomocí náhledu Správce azure firewallu 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Pomocí Azure Firewall Manager Preview můžete vytvořit zabezpečené virtuální rozbočovače pro zabezpečení vašeho cloudového síťového provozu určeného na privátní IP adresy, Azure PaaS a internet. Směrování provozu do brány firewall je automatizované, takže není nutné vytvářet uživatelem definované trasy (UDR).

![zabezpečení cloudové sítě](media/secure-cloud-network/secure-cloud-network.png)

Správce brány firewall také podporuje architekturu virtuální sítě rozbočovače. Porovnání typů architektury virtuálního rozbočovače a rozbočovače najdete v [tématu Jaké jsou možnosti architektury Správce brány Firewall Azure?](vhubs-and-vnets.md)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě paprsku
> * Vytvoření zabezpečeného virtuálního rozbočovače
> * Připojení virtuálních sítí rozbočovače a paprsků
> * Vytvoření zásad brány firewall a zabezpečení centra
> * Směrování provozu do rozbočovače
> * Testování brány firewall

## <a name="create-a-hub-and-spoke-architecture"></a>Vytvoření architektury rozbočovače a paprsku

Nejprve vytvořte virtuální síť pro paprsky, do které můžete umístit servery.

### <a name="create-a-spoke-vnet-and-subnets"></a>Vytvoření virtuální sítě pro paprsky a podsítí

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. V části **Networking**vyberte **Virtuální síť**.
4. Do **pole Název**zadejte **Spoke-01**.
5. V části **Adresní prostor** zadejte **10.0.0.0/16**.
6. V části **Předplatné** vyberte své předplatné.
7. Ve **skupině Prostředků**vyberte možnost Vytvořit **nový**a zadejte příkaz **FW-Manager** a vyberte **OK**.
8. V **případě Umístění**vyberte možnost **(USA) – východní USA**.
9. V **části Podsíť**pro **název** typu **Workload-SN**.
10. V části **Rozsah adres** zadejte **10.0.1.0/24**.
11. Přijměte další výchozí nastavení a pak vyberte **Vytvořit**.

Dále vytvořte podsíť pro server pro přeskoče.

1. Na domovské stránce portálu Azure vyberte **skupiny** > prostředků**FW-Manager**.
2. Vyberte virtuální síť **Spoke-01.**
3. Vyberte **podsítě** > **+podsíť**.
4. Do **pole Název**zadejte příkaz **Jump-SN**.
5. V části **Rozsah adres** zadejte **10.0.2.0/24**.
6. Vyberte **OK**.

### <a name="create-the-secured-virtual-hub"></a>Vytvoření zabezpečeného virtuálního rozbočovače

Vytvořte zabezpečené virtuální centrum pomocí Správce brány firewall.

1. Na domovské stránce portálu Azure vyberte **Všechny služby**.
2. Do vyhledávacího pole zadejte **Správce brány firewall** a vyberte Správce brány **firewall**.
3. Na stránce **Správce brány firewall** vyberte Vytvořit zabezpečené virtuální **centrum**.
4. Na stránce **Vytvořit nové zabezpečené virtuální centrum** vyberte předplatné a skupinu prostředků **FW-Manager.**
5. Pro **název zabezpečeného virtuálního rozbočovače**zadejte **Hub-01**.
6. V **pouzdřite možnost Umístění** **vyberte možnost Východní USA**.
7. Do **adresního prostoru centra**zadejte **10.1.0.0/16**.
8. Pro nový název vWAN zadejte **vwan-01**.
9. Ponechejte zaškrtnutí **políčka Zahrnout bránu VPN a povolit důvěryhodné partnery zabezpečení.**
10. Vyberte **Další:Brána Azure Firewall**.
11. Přijměte výchozí nastavení **Azure Firewall** **Enabled** a pak vyberte **Další: Důvěryhodný partner zabezpečení**.
12. Přijměte výchozí nastavení **Důvěryhodného partnera pro zabezpečení** **zakázáno** a vyberte **další: Revize + vytvoření**.
13. Vyberte **Vytvořit**. Nasazení bude trvat asi 30 minut.

### <a name="connect-the-hub-and-spoke-vnets"></a>Připojení virtuálních sítí rozbočovače a paprsků

Teď můžete peer rozbočovač a paprskové virtuální sítě.

1. Vyberte skupinu prostředků **FW-Manager** a vyberte virtuální wan **vwan-01.**
2. V části **Připojení**vyberte **Možnost Připojení virtuální sítě**.
3. Vyberte **Přidat připojení**.
4. Do **pole Název připojení**zadejte příkaz **hub-spoke**.
5. V **rozbočovačích**vyberte **Hub-01**.
6. V **případě virtuální sítě**vyberte **položku Spoke-01**.
7. Vyberte **OK**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Vytvoření zásad brány firewall a zabezpečení centra

Zásady brány firewall definují kolekce pravidel pro přímé přenosy v jednom nebo více zabezpečených virtuálních rozbočovačích. Vytvoříte zásady brány firewall a pak zabezpečíte své centrum.

1. Ve Správci brány firewall vyberte **Vytvořit zásady brány Azure .**
2. Vyberte předplatné a pak vyberte skupinu prostředků **FW-Manager.**
3. V části **Podrobnosti o zásadách**vyberte v části **Název** **typu Zásada-01** a Pro **oblast** možnost **Východ USA**.
4. Vyberte **Další:Pravidla**.
5. Na kartě **Pravidla** vyberte **Přidat kolekci pravidel**.
6. Na stránce **Přidat kolekci pravidel** zadejte **RC-01** pro **název**.
7. V **popřípadě typ kolekce pravidla**vyberte možnost **Aplikace**.
8. V **případě priority**zadejte **100**.
9. Ujistěte **se, že akce kolekce pravidel** je **Povolit**.
10. Pro pravidlo **Název** typu **Povolit-msft**.
11. Do **pole Zdrojová adresa**zadejte . **\***
12. Do **protokolu**zadejte **http,https**.
13. Ujistěte se, že **Cílový typ je **FQDN**.
14. Do **pole Cíl**zadejte ** \*příkaz .microsoft.com**.
15. Vyberte **Přidat**.
16. Vyberte **další: Zabezpečené virtuální rozbočovače**.
17. Na kartě **Zabezpečené virtuální rozbočovače** vyberte **Hub-01**.
19. Vyberte **Zkontrolovat a vytvořit**.
20. Vyberte **Vytvořit**.

To může trvat asi pět minut nebo více.

## <a name="route-traffic-to-your-hub"></a>Směrování provozu do rozbočovače

Nyní je nutné zajistit, aby se síťový provoz přesměroval přes bránu firewall.

1. Ve Správci brány firewall vyberte **Zabezpečené virtuální rozbočovače**.
2. Vyberte **Hub-01**.
3. V části **Nastavení**vyberte **Nastavení trasy**.
4. V části **Internetový provoz**možnost Provoz **z virtuálních sítí**vyberte **Odeslat přes bránu Azure Firewall**.
5. V části **Privátní provoz Azure**– **provoz do virtuálních sítí**) vyberte **Odeslat přes Bránu Azure**.
6. Vyberte **možnost Upravit předponu IP adres**.
7. Vyberte **možnost Přidat předponu adresy IP**.
8. Jako adresu podsítě Pracovní vytížení zadejte **10.0.1.0/24** a vyberte **Uložit**.
9. V části **Nastavení**vyberte **Možnost Připojení**.
10. Vyberte připojení **s paprskem rozbočovače** a pak vyberte **Zabezpečený internetový provoz** a pak vyberte **OK**.


## <a name="test-your-firewall"></a>Testování brány firewall

Chcete-li otestovat pravidla brány firewall, budete muset nasadit několik serverů. Nasazení workload-srv v podsíti Workload-SN k testování pravidel brány firewall a Jump-Srv, takže můžete použít vzdálenou plochu pro připojení z Internetu a pak se připojit k Workload-Srv.

### <a name="deploy-the-servers"></a>Nasazení serverů

1. Na webu Azure Portal vyberte **Vytvořit prostředek**.
2. V seznamu **Oblíbené** vyberte **Datové centrum Windows Serveru 2016.**
3. Zadejte pro virtuální počítač tyto hodnoty:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Skupina prostředků     |**FW-Manažer**|
   |Název virtuálního počítače     |**Skok-Srv**|
   |Region (Oblast)     |**(USA) východní USA)**|
   |Uživatelské jméno správce     |**azureuser**|
   |Heslo     |zadejte heslo|

4. V části **Pravidla příchozího portu**vyberte v části **Veřejné příchozí porty**možnost **Povolit vybrané porty**.
5. V **části Vybrat příchozí porty**vyberte možnost **RDP (3389).**

6. Přijměte další výchozí hodnoty a vyberte **možnost Další: Disky**.
7. Přijměte výchozí hodnoty disku a vyberte **možnost Další: Síť**.
8. Ujistěte se, že je pro virtuální síť vybrána možnost **Spoke-01** a že podsíť je **Jump-SN**.
9. V **případě veřejné IP adresy**přijměte výchozí nový název veřejné IP adresy (Jump-Srv-ip).
11. Přijměte další výchozí hodnoty a vyberte **Další: Správa**.
12. Chcete-li diagnostiku spouštění zakázat, vyberte **možnost Vypnuto.** Přijměte ostatní výchozí hodnoty a vyberte **Zkontrolovat + vytvořit**.
13. Zkontrolujte nastavení na stránce souhrnu a pak vyberte **Vytvořit**.

Informace v následující tabulce slouží ke konfiguraci jiného virtuálního počítače s názvem **Workload-Srv**. Zbývající část konfigurace je stejná jako u virtuálního počítače Srv-Jump.

|Nastavení  |Hodnota  |
|---------|---------|
|Podsíť|**Workload-SN**|
|Veřejná IP adresa|**Žádné**|
|Veřejné příchozí porty|**Žádné**|

### <a name="add-a-route-table-and-default-route"></a>Přidání tabulky trasy a výchozí trasy

Chcete-li povolit připojení k Internetu do jump-srv, musíte vytvořit směrovací tabulku a výchozí trasu brány do Internetu z podsítě **Jump-SN.**

1. Na webu Azure Portal vyberte **Vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **tabulku postupu** a pak vyberte Možnost **Směrovat tabulku**.
3. Vyberte **Vytvořit**.
4. Zadejte **RT-01** pro **název**.
5. Vyberte předplatné, **FW-Manager** pro skupinu prostředků a **(USA) východní USA** pro oblast.
6. Vyberte **Vytvořit**.
7. Po dokončení nasazení vyberte trasovou tabulku **RT-01.**
8. Vyberte **Trasy** a pak vyberte **Přidat**.
9. Zadejte název **trasy** **na inet** .
10. Zadejte **0.0.0.0/0** pro **předponu Adresa**.
11. Vyberte **Možnost Internet** pro **další typ směrování**.
12. Vyberte **OK**.
13. Po dokončení nasazení vyberte **Podsítě**a pak vyberte **Přidružit**.
14. Vyberte **možnost Spoke-01** pro **virtuální síť**.
15. Vyberte **možnost Jump-SN** pro **podsíť**.
16. Vyberte **OK**.

### <a name="test-the-rules"></a>Otestujte pravidla

Nyní otestujte pravidla brány firewall a potvrďte, že funguje podle očekávání.

1. Na webu Azure portal zkontrolujte nastavení sítě pro virtuální **počítač Workload-Srv** a poznamenejte si privátní IP adresu.
2. Připojte vzdálenou plochu k virtuálnímu počítači **Jump-Srv** a přihlaste se. Odtud otevřete připojení vzdálené plochy k privátní IP adrese **Workload-Srv.**

3. Otevřete prohlížeč Internet Explorer a přejděte na adresu https://www.microsoft.com.
4. V yberte **OK** > **Zavřít** v výstrahách zabezpečení aplikace Internet Explorer.

   Měla by se zobrazit domovská stránka společnosti Microsoft.

5. Přejděte na https://www.google.com.

   Brána firewall by vás měla zablokovat.

Takže teď jste ověřili, že pravidla brány firewall fungují:

* Můžete přejít na jediný povolený plně kvalifikovaný název domény, ale jinam už ne.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o důvěryhodných partnerech zabezpečení](trusted-security-partners.md)
