---
title: Použití Azure Application Gateway k ochraně webových aplikací v řešení VMware Azure
description: Nakonfigurujte Azure Application Gateway, aby bezpečně vystavil vaše webové aplikace běžící na řešení Azure VMware.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 9b10c206114ca922cc11bd8cb0321941b8ba672c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384193"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Použití Azure Application Gateway k ochraně webových aplikací v řešení VMware Azure

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) je nástroj pro vyrovnávání zatížení webového provozu vrstvy 7, který umožňuje spravovat provoz do webových aplikací. Nabízí se v řešení Azure VMware v 1.0 i v 2.0. Obě verze byly testovány s využitím webových aplikací spuštěných v řešení Azure VMware.

Mezi tyto možnosti patří: 
- Spřažení relace na základě souborů cookie
- Směrování na základě adresy URL
- Firewall webových aplikací (WAF)

Úplný seznam funkcí najdete v tématu [funkce Azure Application Gateway](../application-gateway/features.md). 

V tomto článku se dozvíte, jak používat Application Gateway před webovou farmou k ochraně webové aplikace běžící na řešení VMware Azure. 

## <a name="topology"></a>Topologie
Diagram ukazuje, jak Application Gateway slouží k ochraně virtuálních počítačů Azure s IaaS, Azure Virtual Machine Scale Sets nebo místních serverů. Application Gateway zachází s virtuálními počítači řešení Azure VMware jako s místními servery. 

![Diagram znázorňující, jak Application Gateway chránit virtuální počítače Azure IaaS, Azure Virtual Machine Scale Sets nebo místní servery.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Azure Application Gateway je momentálně jedinou podporovanou metodou pro vystavování webových aplikací běžících na virtuálních počítačích řešení VMware Azure.

Diagram znázorňuje scénář testování, který slouží k ověření Application Gateway s využitím webových aplikací řešení Azure VMware.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Diagram znázorňující scénář testování, který slouží k ověření Application Gateway pomocí webových aplikací řešení Azure VMware." border="false":::

Instance Application Gateway je nasazena v centru ve vyhrazené podsíti. Má veřejnou IP adresu Azure. Doporučuje se aktivovat Standard DDoS Protection pro virtuální síť. Webový server je hostovaný na privátním cloudu řešení Azure VMware za NSX T0 a T1 Gateway. Řešení Azure VMware používá [Global REACH ExpressRoute](../expressroute/expressroute-global-reach.md) k umožnění komunikace s rozbočovačem a místními systémy.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným.
- Je nasazený a spuštěný privátní cloud řešení Azure VMware.

## <a name="deployment-and-configuration"></a>Nasazení a konfigurace

1. V Azure Portal vyhledejte **Application Gateway** a vyberte **vytvořit Aplikační bránu**.

2. Zadejte základní podrobnosti, jak je znázorněno na následujícím obrázku. pak vyberte **Další: front-endu>**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Snímek obrazovky se stránkou vytvořit Aplikační bránu v Azure Portal.":::

3. Vyberte typ IP adresy front-endu. Pro možnost veřejné vyberte existující veřejnou IP adresu nebo vytvořte novou. Vyberte **Další: back-endy>**.

    > [!NOTE]
    > U privátních front-endu se podporují jenom SKU Standard a firewall webových aplikací (WAF).

4. Přidejte back-end fond virtuálních počítačů, které běží na infrastruktuře řešení Azure VMware. Zadejte podrobnosti o webových serverech, které běží na privátním cloudu řešení Azure VMware, a vyberte **Přidat**.  Pak vyberte **Další:>konfigurace**.

5. Na kartě **Konfigurace** vyberte **Přidat pravidlo směrování**.

6. Na kartě **naslouchací proces** zadejte podrobnosti pro naslouchací proces. Pokud je vybrána možnost HTTPS, je nutné zadat certifikát ze souboru PFX nebo existujícího certifikátu Azure Key Vault. 

7. Vyberte kartu **cílení na back-end** a vyberte back-end fond, který jste dříve vytvořili. V poli **nastavení protokolu HTTP** vyberte **Přidat nový**.

8. Nakonfigurujte parametry pro nastavení protokolu HTTP. Vyberte **Přidat**.

9. Pokud chcete nakonfigurovat pravidla na základě cest, vyberte **Přidat více cílů pro vytvoření pravidla založeného na cestách**. 

10. Přidejte pravidlo na základě cesty a vyberte **Přidat**. Opakujte pro přidání dalších pravidel založených na cestách. 

11. Až dokončíte přidávání pravidel založených na cestách, vyberte **Přidat** znovu. pak vyberte **Další: značky>**. 

12. Přidejte značky a potom vyberte **Další: zkontrolovat + vytvořit>**.

13. Na Application Gateway se spustí ověřování. Pokud je to úspěšné, vyberte **vytvořit** k nasazení.

## <a name="configuration-examples"></a>Příklady konfigurace

Teď nakonfigurujeme Application Gateway s virtuálními počítači řešení VMware Azure jako back-endové fondy pro tyto případy použití: 

- [Hostování více webů](#hosting-multiple-sites)
- [Směrování podle adresy URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hostování více webů

Tento postup vám ukáže, jak definovat fondy adres back-endu pomocí virtuálních počítačů spuštěných v privátním cloudu řešení Azure VMware v existující aplikační bráně. 

>[!NOTE]
>Tento postup předpokládá, že máte více domén, takže budeme používat příklady www.contoso.com a www.fabrikam.com.


1. V privátním cloudu vytvořte dva různé fondy virtuálních počítačů. Jedna představuje contoso a druhou společnost Fabrikam. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool.png" alt-text="Snímek obrazovky zobrazující souhrn podrobností webového serveru v klientovi VSphere":::

    Používali jsme Windows Server 2016 s nainstalovanou rolí Internetová informační služba (IIS). Po instalaci virtuálních počítačů spusťte následující příkazy PowerShellu ke konfiguraci služby IIS na každém virtuálním počítači. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. V existující instanci služby Application Gateway vyberte v nabídce vlevo **back-end fondy** , vyberte  **Přidat** a zadejte podrobnosti nové fondy. V pravém podokně vyberte **Přidat** .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png" alt-text="Snímek stránky back-end fondů pro přidání back-end fondů" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png":::

3. V části **naslouchací procesy** vytvořte nový naslouchací proces pro každý web. Zadejte podrobnosti každého naslouchacího procesu a vyberte **Přidat**.

4. Na levé straně vyberte **Nastavení http** a v levém podokně vyberte **Přidat** . Vyplňte podrobnosti a vytvořte nové nastavení HTTP a vyberte **Uložit**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png" alt-text="Snímek obrazovky se stránkou nastavení HTTP a vytvořit nové nastavení HTTP." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png":::

5. V levé nabídce v části **pravidla** vytvořte pravidla. Přidružte každé pravidlo k odpovídajícímu naslouchacího procesu. Vyberte **Přidat**.

6. Nakonfigurujte odpovídající back-end fond a nastavení HTTP. Vyberte **Přidat**.

7. Otestujte připojení. Otevřete preferovaný prohlížeč a přejděte na různé weby hostované v prostředí vašeho řešení Azure VMware, například http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-07.png" alt-text="Snímek obrazovky stránky prohlížeče znázorňující úspěšné testování připojení":::

### <a name="routing-by-url"></a>Směrování podle adresy URL

Následující kroky definují back-endové fondy adres pomocí virtuálních počítačů spuštěných v privátním cloudu řešení Azure VMware. Privátní cloud je v existující aplikační bráně. Pak vytvoříte pravidla směrování, která zajistí, že webový provoz dorazí na příslušné servery ve fondech.

1. V privátním cloudu vytvořte fond virtuálních počítačů, který bude představovat webovou farmu. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool.png" alt-text="Snímek stránky v klientovi VMSphere zobrazující souhrn jiného virtuálního počítače":::

    K ilustraci tohoto kurzu jsme použili Windows Server 2016 s nainstalovanou rolí IIS. Po instalaci virtuálních počítačů spusťte následující příkazy PowerShellu ke konfiguraci služby IIS pro každý virtuální počítač. 

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

2. Přidejte tři nové back-end fondy v existující instanci služby Application Gateway. 

   1. V nabídce vlevo vyberte **back-endové fondy** . 
   1. Vyberte **Přidat** a zadejte podrobnosti o prvním fondu, **Contoso-web**. 
   1. Přidejte jako cíl jeden virtuální počítač. 
   1. Vyberte **Přidat**. 
   1. Tento postup opakujte pro **společnosti Contoso-image** a **Contoso-video**, přičemž jako cíl přidejte jeden jedinečný virtuální počítač. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png" alt-text="Snímek obrazovky se stránkou back-end fondů ukazující přidání tří nových back-end fondů." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png":::

3. V části **naslouchací procesy** vytvořte nový naslouchací proces typu Basic pomocí portu 8080.

4. Na levém navigačním panelu vyberte **Nastavení http** a v levém podokně vyberte **Přidat** . Vyplňte podrobnosti a vytvořte nové nastavení HTTP a vyberte **Uložit**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-04.png" alt-text="Snímek obrazovky s nastavením stránky pro přidání nastavení HTTP se zobrazením konfigurace nastavení protokolu HTTP.":::

5. V levé nabídce v části **pravidla** vytvořte pravidla. Přidružte každé pravidlo k dříve vytvořenému naslouchacího procesu. Pak nakonfigurujte hlavní back-end fond a nastavení HTTP. Vyberte **Přidat**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-07.png" alt-text="Snímek obrazovky přidání pravidla směrování pro konfiguraci pravidel směrování pro cíl back-endu":::

6. Otestujte konfiguraci. Přihlaste se k aplikační bráně na Azure Portal a zkopírujte veřejnou IP adresu v části **Přehled** . 

   1. Otevřete nové okno prohlížeče a zadejte adresu URL `http://<app-gw-ip-address>:8080` . 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-08.png" alt-text="Snímek obrazovky stránky prohlížeče znázorňující úspěšný test konfigurace.":::

   1. Změňte adresu URL na `http://<app-gw-ip-address>:8080/images/test.htm`.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-09.png" alt-text="Snímek obrazovky s dalším úspěšným testem s novou adresou URL":::

   1. Změňte adresu URL znovu na `http://<app-gw-ip-address>:8080/video/test.htm` .

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-10.png" alt-text="Snímek obrazovky s úspěšným testem s konečnou adresou URL":::

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s používáním Application Gateway k ochraně webové aplikace běžící na řešení VMware Azure, může být vhodné získat informace o:

- [Konfigurace Azure Application Gateway pro různé scénáře](../application-gateway/configuration-overview.md).
- [Nasazuje se Traffic Manager pro vyrovnávání zatížení řešení Azure VMware](deploy-traffic-manager-balance-workloads.md).
- [Integrace Azure NetApp Files s využitím úloh Azure VMware pro řešení](netapp-files-with-azure-vmware-solution.md).
