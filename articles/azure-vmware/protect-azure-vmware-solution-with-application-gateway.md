---
title: Použití Azure Application Gateway k ochraně webových aplikací v řešení VMware Azure
description: Nakonfigurujte Azure Application Gateway, aby bezpečně vystavil vaše webové aplikace běžící na řešení Azure VMware.
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: b63b7348419500504cb45917d741cc17e2b8a622
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951447"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Použití Azure Application Gateway k ochraně webových aplikací v řešení VMware Azure

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) je nástroj pro vyrovnávání zatížení webového provozu vrstvy 7, který umožňuje spravovat provoz do webových aplikací. Nabízí spoustu možností: spřažení relace na základě souborů cookie, směrování na základě adresy URL a firewall webových aplikací (WAF) pro pojmenování. (Úplný seznam funkcí najdete v tématu [funkce Azure Application Gateway](../application-gateway/features.md).) Je nabízená ve dvou verzích, V1 a v2. Byly testovány s využitím webových aplikací spuštěných v řešení Azure VMware.

V tomto článku Vás provedeme běžným scénářem, který používá Application Gateway před webovou farmou a sadou konfigurací a doporučení pro ochranu webové aplikace běžící na řešení Azure VMware. 

## <a name="topology"></a>Topologie
Jak je znázorněno na následujícím obrázku, Application Gateway lze použít k ochraně virtuálních počítačů Azure s IaaS, služby Azure Virtual Machine Scale Sets nebo místních serverů. Virtuální počítače řešení Azure VMware se budou považovat za místní servery Application Gateway.

![Application Gateway chrání virtuální počítače s řešeními Azure VMware.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Služba Azure Application Gateway je aktuálně jedinou podporovanou metodou pro vystavování webových aplikací běžících na virtuálních počítačích řešení Azure VMware.

Následující diagram znázorňuje scénář testování, který se používá k ověření Application Gateway s webovými aplikacemi řešení Azure VMware.

![Application Gateway integrace s řešením Azure VMware, které spouští webové aplikace.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-avs-scenario.png)

Instance Application Gateway je nasazena v centru ve vyhrazené podsíti. Má veřejnou IP adresu Azure; doporučuje se aktivovat Standard DDoS Protection pro virtuální síť. Webový server je hostovaný v rámci privátního cloudu řešení Azure VMware za NSX T0 a T1. Řešení Azure VMware využívá [ExpressRoute Global REACH](../expressroute/expressroute-global-reach.md) k umožnění komunikace s rozbočovačem a místními systémy.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným.
- Je nasazený a spuštěný privátní cloud řešení Azure VMware.

## <a name="deployment-and-configuration"></a>Nasazení a konfigurace

1. V Azure Portal vyhledejte **Application Gateway** a vyberte **vytvořit Aplikační bránu**.

2. Zadejte základní podrobnosti, jak je znázorněno na následujícím obrázku. pak vyberte **Další: front-endu>**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Vytváření Application Gateway":::

3. Vyberte typ IP adresy front-endu. Pro možnost veřejné vyberte existující veřejnou IP adresu nebo vytvořte novou. Vyberte **Další: back-endy>**.

    > [!NOTE]
    > U privátních front-endu se podporují jenom SKU Standard a firewall webových aplikací (WAF).

4. Dále přidejte back-end fond, který popisuje sadu instancí, které jsou součástí aplikace nebo služby (v tomto případě virtuální počítače běžící v infrastruktuře řešení Azure VMware). Zadejte podrobnosti o webových serverech, které běží na privátním cloudu řešení Azure VMware, a vyberte **Přidat**; pak vyberte **Další:>konfigurace **.

1. Na kartě **Konfigurace** vyberte **Přidat pravidlo směrování**.

6. Na kartě **naslouchací proces** zadejte podrobnosti pro naslouchací proces. Pokud je vybraná možnost HTTPS, musí se zadat certifikát ze souboru PFX nebo ze stávajícího certifikátu z Azure Key Vault. 

7. Vyberte kartu **cílení na back-end** a vyberte back-end fond, který jste dříve vytvořili. V poli **nastavení protokolu HTTP** vyberte **Přidat nový**.

8. Nakonfigurujte parametry pro nastavení protokolu HTTP. Vyberte **Přidat**.

9. Pokud chcete nakonfigurovat pravidla na základě cest, vyberte **Přidat více cílů pro vytvoření pravidla založeného na cestách**. 

10. Přidejte pravidlo na základě cesty a vyberte **Přidat**. Opakujte pro přidání dalších pravidel založených na cestách. 

11. Až dokončíte přidávání pravidel založených na cestách, vyberte **Přidat** znovu. pak vyberte **Další: značky>**. 

12. Přidejte libovolné požadované značky. Vyberte **Další: zkontrolovat + vytvořit>**.

13. Na Application Gateway se spustí ověřování. Pokud je úspěšná, vyberte **vytvořit** a nasaďte.

## <a name="configuration-examples"></a>Příklady konfigurace

V této části se dozvíte, jak nakonfigurovat Application Gateway s virtuálními počítači řešení VMware Azure jako back-endové fondy pro tyto případy použití: 

- [Hostování více webů](#hosting-multiple-sites)
- [Směrování podle adresy URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hostování více webů

Můžete použít Azure Portal ke konfiguraci hostování více webů při vytváření aplikační brány. V tomto kurzu definujete fondy back-end adres pomocí virtuálních počítačů, které jsou spuštěné v privátním cloudu řešení Azure VMware v existující aplikační bráně. Application Gateway je součástí virtuální sítě rozbočovače, jak je popsáno v tématu [integrace řešení Azure VMware v architektuře hub a paprsků](concepts-hub-and-spoke.md). V tomto kurzu se předpokládá, že vlastníte více domén a použijete příklady www.contoso.com a www.fabrikam.com.

1. Vytvořte virtuální počítače. V privátním cloudu řešení Azure VMware vytvořte dva různé fondy virtuálních počítačů. Ten bude představovat společnost Contoso a druhou společnost Fabrikam. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs.png" alt-text="Vytváření Application Gateway":::

    K ilustraci tohoto kurzu jsme použili Windows Server 2016 s nainstalovanou rolí služby Internetová informační služba (IIS). Po instalaci virtuálních počítačů spusťte následující příkazy PowerShellu ke konfiguraci služby IIS na každém virtuálním počítači. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Přidejte back-end fondy. V existující instanci služby Application Gateway vyberte v nabídce vlevo možnost **back-end fondy** , vyberte  **Přidat**a zadejte podrobnosti o nových fondech. V pravém podokně vyberte **Přidat** .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png" alt-text="Vytváření Application Gateway" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png":::

3. V části **naslouchací procesy** vytvořte nový naslouchací proces pro každý web. Zadejte podrobnosti každého naslouchacího procesu a vyberte **Přidat**.

4. Na levém navigačním panelu vyberte **Nastavení http** a v levém podokně vyberte **Přidat** . Vyplňte podrobnosti a vytvořte nové nastavení HTTP a vyberte **Uložit**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png" alt-text="Vytváření Application Gateway" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png":::

5. V levé nabídce v části **pravidla** vytvořte pravidla. Přidružte každé pravidlo k odpovídajícímu naslouchacího procesu. Vyberte **Přidat**.

6. Nakonfigurujte odpovídající back-end fond a nastavení HTTP. Vyberte **Přidat**.

7. Otestujte připojení. Otevřete preferovaný prohlížeč a přejděte na různé weby hostované ve vašem prostředí řešení Azure VMware, například http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-07.png" alt-text="Vytváření Application Gateway":::

### <a name="routing-by-url"></a>Směrování podle adresy URL

Pomocí Azure Application Gateway můžete nakonfigurovat pravidla směrování na základě cest URL. V tomto kurzu definujete fondy back-end adres pomocí virtuálních počítačů, které jsou spuštěné v privátním cloudu řešení Azure VMware v existující aplikační bráně. Application Gateway je součástí virtuální sítě rozbočovače, jak je popsáno v [dokumentaci k Azure VMware Solution Native Integration](concepts-hub-and-spoke.md). Pak vytvoříte pravidla směrování, která zajistí, že webový provoz dorazí na příslušné servery ve fondech.

1. Vytvořte virtuální počítače. V privátním cloudu řešení Azure VMware vytvořte fond virtuálních počítačů, které reprezentují webovou farmu. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs.png" alt-text="Vytváření Application Gateway"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Přidejte back-end fondy. V existující instanci aplikační brány budete muset přidat tři nové back-endové fondy. V nabídce vlevo vyberte **back-endové fondy** . Vyberte **Přidat** a zadejte podrobnosti o prvním fondu, **Contoso-web**. Přidejte jako cíl jeden virtuální počítač. Vyberte **Přidat**. Tento postup opakujte pro **společnosti Contoso-image** a **Contoso-video**a přidejte do každého cíle jeden jedinečný virtuální počítač. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Vytváření Application Gateway" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png":::

3. V části **naslouchací procesy** vytvořte nový naslouchací proces typu Basic pomocí portu 8080.

4. Na levém navigačním panelu vyberte **Nastavení http** a v levém podokně vyberte **Přidat** . Vyplňte podrobnosti a vytvořte nové nastavení HTTP a vyberte **Uložit**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Vytváření Application Gateway":::

5. V levé nabídce v části **pravidla** vytvořte pravidla. Přidružte každé pravidlo k dříve vytvořenému naslouchacího procesu. Pak nakonfigurujte hlavní back-end fond a nastavení HTTP. Vyberte **Přidat**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Vytváření Application Gateway":::

6. Otestujte konfiguraci. Přístup k aplikační bráně na Azure Portal a v části **Přehled** ZKOPÍRUJTE veřejnou IP adresu. Pak otevřete nové okno prohlížeče a zadejte adresu URL `http://<app-gw-ip-address>:8080` . 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Vytváření Application Gateway":::

    Změňte adresu URL na `http://<app-gw-ip-address>:8080/images/test.htm`.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Vytváření Application Gateway":::

    Změňte adresu URL znovu na `http://<app-gw-ip-address>:8080/video/test.htm` .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Vytváření Application Gateway":::

## <a name="next-steps"></a>Další kroky

Další příklady konfigurace najdete v [dokumentaci k Azure Application Gateway](../application-gateway/index.yml) .