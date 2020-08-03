---
title: Použití Azure Application Gateway k ochraně webových aplikací v řešení VMware Azure
description: Nakonfigurujte Azure Application Gateway, aby bezpečně vystavil vaše webové aplikace běžící na řešení Azure VMware (AVS).
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: dfe55ab6b32e9c7b73b8501a16fa6cfaad5bbabe
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2020
ms.locfileid: "87514277"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Použití Azure Application Gateway k ochraně webových aplikací v řešení VMware Azure

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) je nástroj pro vyrovnávání zatížení webového provozu vrstvy 7, který umožňuje spravovat provoz do webových aplikací. Nabízí spoustu možností: spřažení relace na základě souborů cookie, směrování na základě adresy URL a firewall webových aplikací (WAF) pro pojmenování. (Úplný seznam funkcí najdete v tématu [funkce Azure Application Gateway](../application-gateway/features.md).) Je nabízená ve dvou verzích, V1 a v2. Byly testovány s využitím webových aplikací spuštěných v řešení Azure VMware (AVS).

V tomto článku Vás provedeme běžným scénářem, který používá Application Gateway před webovou farmou a sadou konfigurací a doporučení pro ochranu webové aplikace běžící na řešení Azure VMware (AVS). 

## <a name="topology"></a>Topologie
Jak je znázorněno na následujícím obrázku, Application Gateway lze použít k ochraně virtuálních počítačů Azure s IaaS, služby Azure Virtual Machine Scale Sets nebo místních serverů. Virtuální počítače se službou AVS budou na místních serverech považovat za Application Gateway.

![Application Gateway chrání virtuální počítače AVS.](media/protect-avs-web-apps-with-app-gw/app-gateway-protects.png)

> [!IMPORTANT]
> Služba Azure Application Gateway je aktuálně jedinou podporovanou metodou pro vystavování webových aplikací běžících na virtuálních počítačích se službou AVS.

Následující diagram znázorňuje scénář testování, který se používá k ověření Application Gateway u webových aplikací se službou AVS.

![Application Gateway integrace se službou AVS běžící webové aplikace](media/protect-avs-web-apps-with-app-gw/app-gateway-avs-scenario.png)

Instance Application Gateway je nasazena v centru ve vyhrazené podsíti. Má veřejnou IP adresu Azure; doporučuje se aktivovat Standard DDoS Protection pro virtuální síť. Webový server je hostovaný na privátním cloudu služby AVS za NSX T0 a T1. K povolení komunikace s rozbočovačem a místními systémy používá funkci [ExpressRoute Global REACH](../expressroute/expressroute-global-reach.md) .

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným.
- Je nasazený a spuštěný privátní cloud služby AVS.

## <a name="deployment-and-configuration"></a>Nasazení a konfigurace

1. V Azure Portal vyhledejte **Application Gateway** a vyberte **vytvořit Aplikační bránu**.

2. Zadejte základní podrobnosti, jak je znázorněno na následujícím obrázku. pak vyberte **Další: front-endu>**. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/create-app-gateway.png" alt-text="Vytváření Application Gateway":::

3. Vyberte typ IP adresy front-endu. Pro možnost veřejné vyberte existující veřejnou IP adresu nebo vytvořte novou. Vyberte **Další: back-endy>**.

    > [!NOTE]
    > U privátních front-endu se podporují jenom SKU Standard a firewall webových aplikací (WAF).

4. Dále přidejte back-end fond, který popisuje sadu instancí, které jsou součástí aplikace nebo služby (v tomto případě virtuální počítače spuštěné na infrastruktuře služby AVS). Zadejte podrobnosti o webových serverech, které běží na privátním cloudu služby AVS, a vyberte **Přidat**; pak vyberte **Další:>konfigurace **.

1. Na kartě **Konfigurace** vyberte **Přidat pravidlo směrování**.

6. Na kartě **naslouchací proces** zadejte podrobnosti pro naslouchací proces. Pokud je vybraná možnost HTTPS, musí se zadat certifikát ze souboru PFX nebo ze stávajícího certifikátu z Azure Key Vault. 

7. Vyberte kartu **cílení na back-end** a vyberte back-end fond, který jste dříve vytvořili. V poli **nastavení protokolu HTTP** vyberte **Přidat nový**.

8. Nakonfigurujte parametry pro nastavení protokolu HTTP. Vyberte možnost **Přidat**.

9. Pokud chcete nakonfigurovat pravidla na základě cest, vyberte **Přidat více cílů pro vytvoření pravidla založeného na cestách**. 

10. Přidejte pravidlo na základě cesty a vyberte **Přidat**. Opakujte pro přidání dalších pravidel založených na cestách. 

11. Až dokončíte přidávání pravidel založených na cestách, vyberte **Přidat** znovu. pak vyberte **Další: značky>**. 

12. Přidejte libovolné požadované značky. Vyberte **Další: zkontrolovat + vytvořit>**.

13. Na Application Gateway se spustí ověřování. Pokud je úspěšná, vyberte **vytvořit** a nasaďte.

## <a name="configuration-examples"></a>Příklady konfigurace

V této části se dozvíte, jak nakonfigurovat Application Gateway s virtuálními počítači služby AVS jako back-endové fondy pro tyto případy použití: 

- [Hostování více webů](#hosting-multiple-sites)
- [Směrování podle adresy URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hostování více webů

Můžete použít Azure Portal ke konfiguraci hostování více webů při vytváření aplikační brány. V tomto kurzu nadefinujete fondy adres back-endu pomocí virtuálních počítačů, které jsou spuštěné v privátním cloudu služby AVS v existující aplikační bráně. Application Gateway je součástí virtuální sítě rozbočovače, jak je popsáno v tématu [integrace služby AVS v architektuře hub a paprsků](concepts-avs-hub-and-spoke-integration.md). V tomto kurzu se předpokládá, že vlastníte více domén a použijete příklady www.contoso.com a www.fabrikam.com.

1. Vytvořte virtuální počítače. V privátním cloudu pro funkci AVS vytvořte dva různé fondy virtuálních počítačů. Ten bude představovat společnost Contoso a druhou společnost Fabrikam. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs.png" alt-text="Fond webového serveru na funkci AVS":::

    K ilustraci tohoto kurzu jsme použili Windows Server 2016 s nainstalovanou rolí služby Internetová informační služba (IIS). Po instalaci virtuálních počítačů spusťte následující příkazy PowerShellu ke konfiguraci služby IIS na každém virtuálním počítači. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Přidejte back-end fondy. V existující instanci služby Application Gateway vyberte v nabídce vlevo možnost **back-end fondy** , vyberte **Přidat**a zadejte podrobnosti o nových fondech. V pravém podokně vyberte **Přidat** .

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png" alt-text="Konfigurace fondu back-endu" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png":::

3. V části **naslouchací procesy** vytvořte nový naslouchací proces pro každý web. Zadejte podrobnosti každého naslouchacího procesu a vyberte **Přidat**.

4. Na levém navigačním panelu vyberte **Nastavení http** a v levém podokně vyberte **Přidat** . Vyplňte podrobnosti a vytvořte nové nastavení HTTP a vyberte **Uložit**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png" alt-text="Konfigurace nastavení HTP" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png":::

5. V levé nabídce v části **pravidla** vytvořte pravidla. Přidružte každé pravidlo k odpovídajícímu naslouchacího procesu. Vyberte možnost **Přidat**.

6. Nakonfigurujte odpovídající back-end fond a nastavení HTTP. Vyberte možnost **Přidat**.

7. Otestujte připojení. Otevřete preferovaný prohlížeč a přejděte do různých webů hostovaných ve vašem prostředí služby AVS, například http://www.fabrikam.com .

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-07.png" alt-text="Konfigurace back-endu pravidla":::

### <a name="routing-by-url"></a>Směrování podle adresy URL

Pomocí Azure Application Gateway můžete nakonfigurovat pravidla směrování na základě cest URL. V tomto kurzu nadefinujete fondy adres back-endu pomocí virtuálních počítačů, které jsou spuštěné v privátním cloudu služby AVS v existující aplikační bráně. Application Gateway je součástí virtuální sítě rozbočovače, jak je popsáno v [dokumentaci ke službě AVS Azure Native Integration](concepts-avs-hub-and-spoke-integration.md). Pak vytvoříte pravidla směrování, která zajistí, že webový provoz dorazí na příslušné servery ve fondech.

1. Vytvořte virtuální počítače. V privátním cloudu pro funkci AVS vytvořte fond virtuálních počítačů, které budou představovat webovou farmu. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs.png" alt-text="Fond webového serveru na funkci AVS":::

    K ilustraci tohoto kurzu jsme použili Windows Server 2016 s nainstalovanou rolí IIS. Po instalaci virtuálních počítačů spusťte následující příkazy PowerShellu ke konfiguraci služby IIS pro kurz v každém virtuálním počítači. 

    První virtuální počítač contoso-web-01 bude hostovat hlavní Web.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    Druhý virtuální počítač contoso-web-02 bude hostovat web imagí.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    Třetí virtuální počítač contoso-web-03 bude hostovat web videa.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Přidejte back-end fondy. V existující instanci aplikační brány budete muset přidat tři nové back-endové fondy. V nabídce vlevo vyberte **back-endové fondy** . Vyberte **Přidat** a zadejte podrobnosti o prvním fondu, **Contoso-web**. Přidejte jako cíl jeden virtuální počítač. Vyberte možnost **Přidat**. Tento postup opakujte pro **společnosti Contoso-image** a **Contoso-video**a přidejte do každého cíle jeden jedinečný virtuální počítač. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Vytvoření fondu back-endu" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png":::

3. V části **naslouchací procesy** vytvořte nový naslouchací proces typu Basic pomocí portu 8080.

4. Na levém navigačním panelu vyberte **Nastavení http** a v levém podokně vyberte **Přidat** . Vyplňte podrobnosti a vytvořte nové nastavení HTTP a vyberte **Uložit**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Konfigurace nastavení HTP":::

5. V levé nabídce v části **pravidla** vytvořte pravidla. Přidružte každé pravidlo k dříve vytvořenému naslouchacího procesu. Pak nakonfigurujte hlavní back-end fond a nastavení HTTP. Vyberte možnost **Přidat**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Konfigurace back-endu pravidla":::

6. Otestujte konfiguraci. Přístup k aplikační bráně na Azure Portal a v části **Přehled** ZKOPÍRUJTE veřejnou IP adresu. Pak otevřete nové okno prohlížeče a zadejte adresu URL `http://<app-gw-ip-address>:8080` . 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Test konfigurace":::

    Změňte adresu URL na `http://<app-gw-ip-address>:8080/images/test.htm`.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Test konfigurace":::

    Změňte adresu URL znovu na `http://<app-gw-ip-address>:8080/video/test.htm` .

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Test konfigurace":::

## <a name="next-steps"></a>Další kroky

Další příklady konfigurace najdete v [dokumentaci k Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) .
