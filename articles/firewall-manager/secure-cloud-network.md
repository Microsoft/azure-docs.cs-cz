---
title: 'Kurz: zabezpečení virtuálního centra pomocí správce Azure Firewall'
description: V tomto kurzu se naučíte zabezpečit virtuální rozbočovač pomocí nástroje Azure Firewall Manager pomocí Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c44daa67b4029c73c57ca82d72ee0a9759dd4c2d
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563660"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Kurz: zabezpečení virtuálního centra pomocí správce Azure Firewall

Pomocí správce Azure Firewall můžete vytvořit zabezpečená virtuální centra pro zabezpečení síťového provozu určeného na privátní IP adresy, do Azure PaaS a na Internet. Směrování provozu do brány firewall je automatizované, takže není nutné vytvářet trasy definované uživatelem (udr).

![zabezpečení cloudové sítě](media/secure-cloud-network/secure-cloud-network.png)

Správce brány firewall taky podporuje architekturu virtuální sítě rozbočovače. Porovnání typů architektury zabezpečeného virtuálního centra a centra virtuálních sítí najdete v tématu [co jsou možnosti architektury Azure firewall Manageru?](vhubs-and-vnets.md)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě paprsků
> * Vytvoření zabezpečeného virtuálního rozbočovače
> * Připojit střed a paprskový virtuální sítě
> * Vytvoření zásady brány firewall a zabezpečení centra
> * Směrování provozu do vašeho centra
> * Testování brány firewall

## <a name="create-a-hub-and-spoke-architecture"></a>Vytvoření architektury centra a paprsků

Nejdřív vytvořte virtuální síť paprsků, kam můžete umístit své servery.

### <a name="create-a-spoke-virtual-network-and-subnets"></a>Vytvoření virtuální sítě a podsítí paprsků

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. V části **sítě**vyberte **virtuální síť**.
2. V části **Předplatné** vyberte své předplatné.
1. V poli **Skupina prostředků**vyberte **vytvořit novou**a jako název zadejte **FW-Manager** a vyberte **OK**.
2. Jako **název**zadejte **paprsek-01**.
3. V **oblasti oblast**vyberte **(US) východní USA**.
4. Vyberte **Další: IP adresy**.
1. V poli **adresní prostor**přijměte výchozí **10.0.0.0/16**.
3. V části **název podsítě**vyberte **výchozí**.
4. Změňte název podsítě na **úlohu-SN**.
5. Jako **Rozsah adres podsítě**zadejte **10.0.1.0/24**.
6. Vyberte **Uložit**...

Dále vytvořte podsíť pro server skoků.

1. Vyberte **Přidat podsíť**.
4. Jako **název podsítě**zadejte **skok-SN**.
5. Jako **Rozsah adres podsítě**zadejte **10.0.2.0/24**.
6. Vyberte **Přidat**.

Nyní vytvořte virtuální síť.

1. Vyberte **Zkontrolovat a vytvořit**.
2. Vyberte **Vytvořit**.

### <a name="create-the-secured-virtual-hub"></a>Vytvoření zabezpečeného virtuálního centra

Vytvořte zabezpečeného virtuálního centra pomocí Správce brány firewall.

1. Na domovské stránce Azure Portal vyberte **všechny služby**.
2. Do vyhledávacího pole zadejte **Správce brány** firewall a vyberte **Správce brány firewall**.
3. Na stránce **Správce brány firewall** vyberte **Zobrazit zabezpečená virtuální centra**.
4. **Správce brány firewall | Na stránce zabezpečená virtuální** centra vyberte **vytvořit nové zabezpečené virtuální rozbočovače**.
5. Jako **skupinu prostředků**vyberte **FW-Manager**.
7. V **oblasti oblast**vyberte **východní USA**.
1. V poli **název zabezpečeného virtuálního centra**zadejte příkaz **hub-01**.
2. Jako **adresní prostor centra**zadejte **10.1.0.0/16**.
3. Pro nový název vWAN zadejte **vWAN-01**.
4. Zrušte zaškrtnutí políčka **Zahrnout bránu VPN pro povolení důvěryhodných partnerů zabezpečení** .
5. Vyberte **Další: Azure firewall**.
6. Přijměte výchozí nastavení **povoleno** **Azure firewall** a pak vyberte **Další: důvěryhodný bezpečnostní partner**.
7. Přijměte výchozí nastavení **zakázaný** **důvěryhodný partner zabezpečení** a vyberte **Další: zkontrolovat + vytvořit**.
8. Vyberte **Vytvořit**. Nasazení bude trvat přibližně 30 minut.

### <a name="connect-the-hub-and-spoke-vnets"></a>Připojit střed a paprskový virtuální sítě

Nyní můžete vytvořit partnerský vztah k rozbočovači a paprsku virtuální sítě.

1. Vyberte skupinu prostředků **FW-Manager** a potom vyberte virtuální síť WAN **Vwan-01** .
2. V části **připojení**vyberte **připojení k virtuální síti**.
3. Vyberte **Přidat připojení**.
4. Jako **název připojení**zadejte **hub-paprsek**.
5. V případě **rozbočovačů**vyberte možnost **hub-01**.
6. Jako **skupinu prostředků**vyberte **FW-Manager**.
7. V případě **virtuální sítě**vyberte **paprskový-01**.
8. Vyberte **Vytvořit**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Vytvoření zásady brány firewall a zabezpečení centra

Zásady brány firewall definují kolekce pravidel pro směrování provozu na jednom nebo několika zabezpečených virtuálních rozbočovačích. Vytvoříte zásadu brány firewall a pak zabezpečíte své centrum.

1. V nástroji Správce brány firewall vyberte **zobrazit Azure firewall zásady**.
2. Vyberte **vytvořit Azure firewall zásady**.
3. V části **Podrobnosti o zásadách**zadejte **název** **zásady typu-01** a v **oblasti** vyberte **východní USA**.
4. Vyberte **Další: pravidla**.
5. Na kartě **pravidla** vyberte **přidat kolekci pravidel**.
6. Na stránce **přidat kolekci pravidel** zadejte **RC-01** pro **název**.
7. Jako **typ kolekce pravidel**vyberte **Application (aplikace**).
8. Jako **Priorita**zadejte **100**.
9. Zajistěte, aby **akce kolekce pravidel** byla **povolená**.
10. Jako **název** pravidla zadejte **Allow-MSFT**.
11. Jako **typ zdroje**vyberte **IP adresa**.
12. Jako **zdroj**zadejte **\*** .
13. V případě **protokolu**zadejte **http, https**.
14. Ujistěte se, že **cílový typ** je **plně kvalifikovaný název domény**.
15. Pro **cíl**zadejte ** \* . Microsoft.com**.
16. Vyberte **Přidat**.
17. Vyberte **Další: Analýza hrozeb**.
18. Vyberte **Další: rozbočovače**.
19. Na kartě **centra** vyberte **přidružit virtuální rozbočovače**.
20. Vyberte možnost **hub-01** a pak vyberte **Přidat**.
21. Vyberte **Zkontrolovat a vytvořit**.
22. Vyberte **Vytvořit**.

To může trvat přibližně pět minut nebo i déle.

## <a name="route-traffic-to-your-hub"></a>Směrování provozu do vašeho centra

Nyní je nutné zajistit, aby byl síťový provoz směrován přes bránu firewall.

1. V nástroji Správce brány firewall vyberte **Zabezpečená virtuální rozbočovače**.
2. Vyberte možnost **hub-01**.
3. V části **Nastavení**vyberte **Konfigurace zabezpečení**.
4. V části **internetový provoz**vyberte **Azure firewall**.
5. V části **privátní přenos**vyberte **Odeslat prostřednictvím Azure firewall**.
10. Ověřte, že připojení **hub-paprsek** zobrazuje **internetový provoz** jako **zabezpečený**.
11. Vyberte **Uložit**.


## <a name="test-your-firewall"></a>Otestování brány firewall

Chcete-li otestovat pravidla brány firewall, budete muset nasadit několik serverů. Nasadíte úlohu – SRV v podsíti zatížení SÉRIOVÉho zatížení, abyste otestovali pravidla brány firewall a mohli přejít na adresu SRV, abyste se mohli připojit z Internetu pomocí vzdálené plochy a pak se připojit k úloze – SRV.

### <a name="deploy-the-servers"></a>Nasazení serverů

1. V Azure Portal vyberte **vytvořit prostředek**.
2. V seznamu **oblíbených** vyberte **Windows Server 2016 Datacenter** .
3. Zadejte pro virtuální počítač tyto hodnoty:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Skupina prostředků     |**FW – správce**|
   |Název virtuálního počítače     |**Skok – SRV**|
   |Oblast     |**VYLEPŠENÍ Východní USA)**|
   |Uživatelské jméno správce     |Zadejte uživatelské jméno.|
   |Heslo     |Zadejte heslo.|

4. V části **pravidla příchozího portu**pro **veřejné příchozí porty**vyberte **Povolit vybrané porty**.
5. V případě **vyberte příchozí porty**vyberte **RDP (3389)**.
6. Přijměte ostatní výchozí hodnoty a vyberte **Další: disky**.
7. Přijměte výchozí nastavení disku a vyberte **Další: sítě**.
8. Ujistěte se, že je pro virtuální síť vybraná možnost **paprske-01** a že podsíť **přeskočí SN**.
9. Pro **veřejnou IP**adresu přijměte výchozí název nové veřejné IP adresy (Přejít-SRV-IP).
11. Přijměte ostatní výchozí hodnoty a vyberte **Další: Správa**.
12. Výběrem možnosti **vypnuto** zakážete diagnostiku spouštění. Přijměte ostatní výchozí hodnoty a vyberte **zkontrolovat + vytvořit**.
13. Zkontrolujte nastavení na stránce Souhrn a pak vyberte **vytvořit**.

Pomocí informací v následující tabulce můžete nakonfigurovat jiný virtuální počítač s názvem úloha **-SRV**. Zbývající část konfigurace je stejná jako u virtuálního počítače Srv-Jump.

|Nastavení  |Hodnota  |
|---------|---------|
|Podsíť|**Workload-SN**|
|Veřejná IP adresa|**Žádné**|
|Veřejné příchozí porty|**Žádné**|

### <a name="add-a-route-table-and-default-route"></a>Přidání směrovací tabulky a výchozí trasy

Pokud chcete, aby připojení k Internetu mohlo přejít k síti Internet, musíte vytvořit směrovací tabulku a výchozí bránu trasy k Internetu z podsítě **skok-SN** .

1. V Azure Portal vyberte **vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **směrovací tabulku** a pak vyberte **směrovací tabulka**.
3. Vyberte **Vytvořit**.
4. Jako **název**zadejte **RT-01** .
5. Vyberte své předplatné, **správce programu FW** pro skupinu prostředků a **(US) východní USA** pro oblast.
6. Vyberte **Vytvořit**.
7. Po dokončení nasazení vyberte tabulku směrování **RT-01** .
8. Vyberte **trasy** a pak vyberte **Přidat**.
9. Jako **název trasy**zadejte **skočit-to-inet** .
10. Jako **předponu adresy**zadejte **0.0.0.0/0** .
11. Jako **typ dalšího segmentu směrování**vyberte **Internet** .
12. Vyberte **OK**.
13. Po dokončení nasazení vyberte **podsítě**a pak vyberte **přidružit**.
14. Vyberte **paprskový-01** pro **virtuální síť**.
15. Vyberte **skok – SN** pro **podsíť**.
16. Vyberte **OK**.

### <a name="test-the-rules"></a>Testování pravidel

Nyní otestujte pravidla brány firewall a potvrďte, že funguje podle očekávání.

1. V Azure Portal zkontrolujte nastavení sítě pro virtuální počítač **SRV** a zaznamenejte si privátní IP adresu.
2. Připojte vzdálenou plochu k virtuálnímu počítači **skoku** a přihlaste se. Odtud otevřete připojení ke vzdálené ploše v rámci **úlohy-SRV** privátní IP adresa.

3. Otevřete prohlížeč Internet Explorer a přejděte na adresu https://www.microsoft.com.
4. **OK**  >  V okně výstrahy zabezpečení aplikace Internet Explorer vyberte OK**Zavřít** .

   Měla by se zobrazit Domovská stránka společnosti Microsoft.

5. Přejděte na https://www.google.com.

   Brána firewall by vás měla zablokovat.

Takže teď ověříte, že pravidla brány firewall fungují:

* Můžete přejít na jediný povolený plně kvalifikovaný název domény, ale jinam už ne.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o důvěryhodných partnerech zabezpečení](trusted-security-partners.md)
