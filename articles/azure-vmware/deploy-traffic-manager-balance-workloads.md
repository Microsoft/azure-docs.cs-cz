---
title: Nasazení Traffic Manager pro vyrovnávání zatížení Azure VMware Solution (AVS)
description: Naučte se integrovat Traffic Manager se službou Azure VMware Solution (AVS) pro vyrovnávání zatížení aplikací napříč několika koncovými body v různých oblastech.
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: 076d9c77d68df3d8acb7b531b3dfbea40fb3cedd
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593114"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>Nasazení Traffic Manager pro vyrovnávání zatížení Azure VMware Solution (AVS)

Tento článek vás provede integrací Traffic Manager s využitím řešení Azure VMware (AVS) pro vyrovnávání zatížení aplikací napříč několika koncovými body. Podíváme se na scénář, ve kterém Traffic Manager směrovat provoz mezi třemi aplikačními branami, které pokrývá několik oblastí služby AVS: Západní USA, Západní Evropa a místní v Východní USA. 

Azure Traffic Manager je nástroj pro vyrovnávání zatížení založený na DNS, který umožňuje distribuci provozu optimálně do služeb napříč globálními oblastmi Azure. Vyrovnává zatížení aplikačních dat napříč úlohami spuštěnými v Azure i s externími veřejnými koncovými body. Další informace o Traffic Manager najdete v tématu [co je Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

Nejdříve si přečtěte [požadavky](#prerequisites) . pak projdeme postupy pro:

> [!div class="checklist"]
> * Ověření konfigurace bran Application Gateway
> * Ověřit konfiguraci segmentu NSX-T
> * Vytvořit profil Traffic Manager
> * Přidání externích koncových bodů do profilu Traffic Manager

## <a name="topology"></a>Topologie

Jak je znázorněno na následujícím obrázku, Azure Traffic Manager poskytuje vyrovnávání zatížení pro aplikace na úrovni DNS mezi místními koncovými body. Aplikační brány mají členy fondu back-end nakonfigurovaní jako servery IIS a odkazují na ně jako externí koncové body služby AVS.

Připojení k virtuální síti mezi dvěma oblastmi privátního cloudu služby AVS, Západní USA a Západní Evropa a místním serverem v Východní USA používá bránu ExpressRoute.   

![Diagram architektury Traffic Manager integrace s řešením Azure VMware](media/traffic-manager/traffic-manager-topology.png)
 
## <a name="prerequisites"></a>Požadavky

- Tři virtuální počítače nakonfigurované jako servery Microsoft IIS spuštěné v různých oblastech služby AVS: Západní USA, Západní Evropa a místní. 

- Aplikační brána s externími koncovými body v Západní USA, Západní Evropa a místně.

- Hostitel s připojením k Internetu pro ověřování. 

## <a name="verify-configuration-of-your-application-gateways"></a>Ověření konfigurace bran Application Gateway

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) je nástroj pro vyrovnávání zatížení webového provozu vrstvy 7, který umožňuje spravovat provoz do webových aplikací. Další informace o Application Gateway najdete v tématu [co je Azure Application Gateway?](../application-gateway/overview.md) 

V tomto scénáři se tři instance aplikační brány konfigurují jako externí koncové body služby AVS. Brány Application Gateway mají virtuální počítače, které jsou nakonfigurované jako členy fondu back-end, k vyrovnávání zatížení příchozích požadavků vrstvy 7. (Informace o tom, jak nakonfigurovat Application Gateway s virtuálními počítači služby AVS jako back-end fondy, najdete v tématu [použití Azure Application Gateway k ochraně webových aplikací v řešení VMware Azure](protect-azure-vmware-solution-with-application-gateway.md).)  

Následující postup ověří správnou konfiguraci bran Application Gateway.

1. Otevřete Azure Portal a vyberte **aplikační brány** , abyste zobrazili seznam aktuálních aplikačních bran. 

    V tomto scénáři jsme nakonfigurovali tři aplikační brány:
    - AVS-GS-WUS
    - AVS-GS-EUS (místní)
    - AVS-GS-ZEU

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Snímek obrazovky stránky Application Gateway zobrazující seznam nakonfigurovaných aplikačních bran" lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. Vyberte jednu z dříve nasazených aplikačních bran. Otevře se okno s informacemi o různých informacích o aplikační bráně. Vyberte **back-end fondy** a ověřte konfiguraci jednoho ze skupin back-end.

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Snímek obrazovky stránky Application Gateway zobrazující podrobnosti vybrané aplikační brány" lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. V tomto případě uvidíme jeden člen back-endu virtuálního počítače nakonfigurovaný jako webový server s IP adresou 172.29.1.10.
 
    :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Snímek stránky pro úpravu back-endu back-endu s zvýrazněnou cílovou IP adresou.":::

    Podobně můžete ověřit konfiguraci ostatních aplikačních bran a členů fondu back-end. 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>Ověřit konfiguraci segmentu NSX-T

Síťové segmenty vytvořené ve Správci NSX-T se používají jako sítě pro virtuální počítače v vCenter. Další informace najdete v kurzu [Vytvoření síťového segmentu NSX-T v řešení Azure VMware (AVS)](tutorial-nsx-t-network-segment.md).

V našem scénáři je segment NSX-T nakonfigurovaný v prostředí služby AVS, kde je připojený členský virtuální počítač back-end fondu.

1. Vyberte **segmenty** pro zobrazení konfigurovaných segmentů. V tomto případě uvidíme, že contoso-SEGMENT1 je připojený k bráně contoso-T01, což je flexibilní směrovač úrovně 1.

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Snímek obrazovky zobrazující profily segmentů ve Správci NSX-T":::    

2. Vyberte **brány úrovně 1** , abyste viděli seznam bran-1 s počtem propojených segmentů. Vyberte segment propojený s contoso-T01. Otevře se okno znázorňující logické rozhraní nakonfigurované na směrovači vrstvy 1. Slouží jako brána k virtuálnímu počítači back-end fondu připojenému k segmentu.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Snímek obrazovky zobrazující adresu brány vybraného segmentu":::    

3. V klientovi vSphere virtuálního počítače vyberte virtuální počítač, na kterém chcete zobrazit jeho podrobnosti. Všimněte si, že IP adresa se shoduje s tím, co jsme viděli v kroku 3 předchozí části: 172.29.1.10.

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Snímek obrazovky zobrazující podrobnosti o virtuálním počítači v klientovi VSphere":::    

4. Vyberte virtuální počítač a pak klikněte na **akce > upravit nastavení** a ověřte připojení k segmentu NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Vytvořit profil Traffic Manager

1. Přihlaste se k [Azure Portal](https://rc.portal.azure.com/#home). V části **služby Azure > sítě** vyberte **profily Traffic Manager**.

2. Vyberte **+ Přidat** a vytvořte nový profil Traffic Manager.
 
3. Zadejte název profilu, metodu směrování (v tomto scénáři budeme používat váženého), podívejte se na [Traffic Manager metody směrování](../traffic-manager/traffic-manager-routing-methods.md)), předplatné a skupinu prostředků a vyberte **vytvořit**.

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Přidání externích koncových bodů do profilu Traffic Manager

1. V podokně výsledků hledání vyberte profil Traffic Manager, vyberte **koncové body** a potom **+ Přidat**.

2. Zadejte požadované podrobnosti: typ, název, plně kvalifikovaný název domény (FQDN) nebo IP a váhu (v tomto scénáři přiřadíme váhu 1 ke každému koncovému bodu). Vyberte **Přidat**. Tím se vytvoří externí koncový bod. Stav monitorování musí být **online**. Opakujte stejný postup pro vytvoření dvou externích koncových bodů, jeden v jiné oblasti a jiné místní. Po vytvoření se všechny tři zobrazí v profilu Traffic Manager a stav všech tří by měl být **online**.

3. Vyberte **Přehled**. Zkopírujte adresu URL v části **název DNS**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Snímek obrazovky s přehledem Traffic Managerho koncového bodu se zvýrazněným názvem DNS"::: 

4. Vložte adresu URL názvu DNS do prohlížeče. Následující snímek obrazovky ukazuje provoz, který směruje do Západní Evropa oblasti.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Snímek obrazovky okna prohlížeče zobrazující provoz směrovaný na Západní Evropa."::: 

5. Aktualizujte si stránku v prohlížeči. Následující snímek obrazovky ukazuje provoz, který je nyní přesměrován do jiné sady členů fondu back-end v oblasti Západní USA.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Snímek obrazovky okna prohlížeče zobrazující provoz směrovaný na Západní USA."::: 

6. Znovu aktualizujte prohlížeč. Následující snímek obrazovky ukazuje provoz, který teď směruje na konečnou sadu členů fondu back-end objektů.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Snímek obrazovky okna prohlížeče zobrazující provoz směrovaný do místního prostředí.":::

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace:

- [Použití Azure Application Gateway v řešení VMware Azure (AVS)](protect-azure-vmware-solution-with-application-gateway.md)
- [Metody směrování Traffic Manageru](../traffic-manager/traffic-manager-routing-methods.md)
- [Kombinování služeb vyrovnávání zatížení v Azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Měření výkonu Traffic Manager](../traffic-manager/traffic-manager-performance-considerations.md)
