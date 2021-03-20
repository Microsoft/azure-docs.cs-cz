---
title: Nasazení Traffic Manager pro vyrovnávání zatížení řešení Azure VMware
description: Naučte se integrovat Traffic Manager s řešením Azure VMware a vyrovnávat aplikační úlohy napříč několika koncovými body v různých oblastech.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 46570c5a61fc0a641d83126fd0f8ef35b3dc42cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988587"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Nasazení Traffic Manager pro vyrovnávání zatížení řešení Azure VMware

Tento článek vás seznámí s postupem, jak integrovat [Traffic Manager Azure](../traffic-manager/traffic-manager-overview.md) s řešením Azure VMware. Integrace vyrovnává aplikační úlohy napříč několika koncovými body. Tento článek vás také provede postupem konfigurace Traffic Manager pro směrování provozu mezi třemi [Azure Application Gateway](../application-gateway/overview.md) zahrnující několik oblastí řešení Azure VMware. 

Brány mají virtuální počítače (VM) Azure VMware nakonfigurované jako členy fondu back-end pro vyrovnávání zatížení příchozích požadavků vrstvy 7. Další informace najdete v tématu [použití Azure Application Gateway k ochraně vašich webových aplikací v řešení VMware Azure](protect-azure-vmware-solution-with-application-gateway.md) .

Diagram ukazuje, jak Traffic Manager poskytuje vyrovnávání zatížení pro aplikace na úrovni DNS mezi místními koncovými body. Brány mají členy fondu back-end nakonfigurovaní jako servery IIS a odkazují jako externí koncové body řešení Azure VMware. Připojení přes virtuální síť mezi dvěma oblastmi privátního cloudu používá bránu ExpressRoute.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagram architektury Traffic Manager integrace s řešením Azure VMware" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Než začnete, nejdříve si přečtěte [předpoklady](#prerequisites) a pak Projděte postupy, které vám pomůžou:

> [!div class="checklist"]
> * Ověřte konfiguraci bran Application Gateway a segmentu NSX-T.
> * Vytvořit profil Traffic Manager
> * Přidání externích koncových bodů do profilu Traffic Manager

## <a name="prerequisites"></a>Předpoklady

- Tři virtuální počítače nakonfigurované jako servery Microsoft IIS běžící v různých oblastech řešení Azure VMware: 
   - USA – západ
   - West Europe
   - Východní USA (místní) 

- Aplikační brána s externími koncovými body v oblastech řešení Azure VMware uvedených výše.

- Hostitel s připojením k Internetu pro ověřování. 

- [Segment sítě NSX-T vytvořený v řešení Azure VMware](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Ověření konfigurace bran Application Gateway

Následující kroky ověřují konfiguraci aplikačních bran.

1. V Azure Portal vyberte **aplikační brány** a zobrazte seznam aktuálních aplikačních bran:

   - AVS-GS-WUS
   - AVS-GS-EUS (místní)
   - AVS-GS-ZEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Snímek obrazovky stránky Application Gateway zobrazující seznam nakonfigurovaných aplikačních bran" lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Vyberte jednu z dříve nasazených aplikačních bran. 

   Otevře se okno s informacemi o různých informacích o aplikační bráně. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Snímek obrazovky stránky Application Gateway zobrazující podrobnosti vybrané aplikační brány" lightbox="media/traffic-manager/backend-pool-config.png":::

1. Vyberte **back-end fondy** a ověřte konfiguraci jednoho ze skupin back-end. Vidíte jeden člen fondu back-endu virtuálního počítače nakonfigurovaný jako webový server s IP adresou 172.29.1.10.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Snímek stránky pro úpravu back-endu back-endu s zvýrazněnou cílovou IP adresou.":::

1. Ověřte konfiguraci ostatních aplikačních bran a členů fondu back-end. 

## <a name="verify-the-nsx-t-segment-configuration"></a>Ověřit konfiguraci segmentu NSX-T

Následující kroky ověřují konfiguraci segmentu NSX-T v prostředí řešení Azure VMware.

1. Vyberte **segmenty** pro zobrazení konfigurovaných segmentů.  Zobrazí se contoso-SEGMENT1 připojený k bráně contoso-T01, což je flexibilní směrovač úrovně 1.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Snímek obrazovky zobrazující profily segmentů ve Správci NSX-T" lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Vyberte **brány úrovně 1** , abyste viděli seznam bran-1 s počtem propojených segmentů. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Snímek obrazovky zobrazující adresu brány vybraného segmentu":::    

1. Vyberte segment propojený s contoso-T01. Otevře se okno znázorňující logické rozhraní nakonfigurované na směrovači vrstvy 1. Slouží jako brána k virtuálnímu počítači, který je členem fondu back-end připojený k segmentu.

1. V klientovi vSphere vyberte virtuální počítač, pro který chcete zobrazit jeho podrobnosti. 

   >[!NOTE]
   >Jeho IP adresa se shoduje s členem fondu back-end virtuálního počítače nakonfigurovaným jako webový server z předchozí části: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Snímek obrazovky zobrazující podrobnosti o virtuálním počítači v klientovi vSphere" lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Vyberte virtuální počítač a pak vyberte **akce > upravit nastavení** a ověřte připojení k segmentu NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Vytvořit profil Traffic Manager

1. Přihlaste se na [Azure Portal](https://rc.portal.azure.com/#home). V části **služby Azure > sítě** vyberte **profily Traffic Manager**.

2. Vyberte **+ Přidat** a vytvořte nový profil Traffic Manager.
 
3. Zadejte následující informace a pak vyberte **vytvořit**:

   - Název profilu
   - Metoda směrování (použijte [Vážený](../traffic-manager/traffic-manager-routing-methods.md)
   - Předplatné
   - Skupina prostředků

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Přidání externích koncových bodů do profilu Traffic Manager

1. V podokně výsledků hledání vyberte profil Traffic Manager, vyberte **koncové body** a potom **+ Přidat**.

1. Pro každý z externích koncových bodů v různých oblastech zadejte požadované podrobnosti a pak vyberte **Přidat**: 
   - Typ
   - Name
   - Plně kvalifikovaný název domény (FQDN) nebo IP adresa
   - Váha (přiřaďte váhu 1 ke každému koncovému bodu). 

   Po vytvoření budou všechny tři zobrazovat v profilu Traffic Manager. Stav monitorování všech tří musí být **online**.

3. V části **název DNS** vyberte **Přehled** a zkopírujte adresu URL.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Snímek obrazovky s přehledem Traffic Managerho koncového bodu se zvýrazněným názvem DNS" lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Vložte adresu URL názvu DNS do prohlížeče. Snímek obrazovky ukazuje provoz, který směruje do Západní Evropa oblasti.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Snímek obrazovky okna prohlížeče zobrazující provoz směrovaný na Západní Evropa."::: 

5. Aktualizujte si stránku v prohlížeči. Snímek obrazovky ukazuje provoz, který směruje na jinou sadu členů fondu back-end v oblasti Západní USA.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Snímek obrazovky okna prohlížeče zobrazující provoz směrovaný na Západní USA."::: 

6. Znovu aktualizujte prohlížeč. Snímek obrazovky ukazuje provoz, který směřuje do konečné sady členů fondu back-end v místním prostředí.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Snímek obrazovky okna prohlížeče zobrazující provoz směrovaný do místního prostředí.":::

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s integrací Azure Traffic Manager s řešením Azure VMware, budete možná chtít získat informace o:

- [Použití azure Application Gateway v řešení VMware Azure](protect-azure-vmware-solution-with-application-gateway.md).
- [Traffic Manager metody směrování](../traffic-manager/traffic-manager-routing-methods.md).
- [Kombinování služeb vyrovnávání zatížení v Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- [Měření výkonu Traffic Manager](../traffic-manager/traffic-manager-performance-considerations.md).
