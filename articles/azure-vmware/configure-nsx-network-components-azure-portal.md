---
title: Konfigurace síťových součástí NSX v řešení Azure VMware
description: Naučte se používat konzolu řešení Azure VMware ke konfiguraci segmentů sítě NSX-T.
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a4702efe8ea26234d074f421d2e6eab871eba5f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105733091"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>Konfigurace síťových součástí NSX v řešení Azure VMware

Privátní cloud řešení Azure VMware je ve výchozím nastavení součástí NSX-T jako softwarově definovaná síť (SDDC). Je dodáván předem zřízené s bránou NSX-T úrovně 0 v **aktivním/aktivním** režimu a výchozí bránou NSX-t vrstvy 1 v aktivním nebo pohotovostním režimu.  Tyto brány umožňují propojení segmentů (logických přepínačů) a poskytování East-West a North-South připojení. 

Po nasazení privátního cloudu řešení Azure VMware můžete nakonfigurovat potřebné objekty NSX-T z konzoly řešení Azure VMware v části **práce s úlohami**.  Konzola nabízí zjednodušený pohled na operace NSX-T, které správce VMware potřebuje denně a cílí na uživatele, kteří nejsou obeznámení s NSX-T.  

K dispozici máte čtyři možnosti konfigurace komponent NSX-T v konzole řešení Azure VMware:
- **Segmenty** – vytváření segmentů, které se zobrazují v NSX-T Manageru a vCenter.
- **DHCP** – Pokud plánujete používat protokol DHCP, vytvořte server DHCP nebo službu DHCP Relay.
- **Zrcadlení portů** – umožňuje vytvořit zrcadlení portů, které vám umožní řešit problémy se sítí.
- **DNS** – vytvoření služby DNS Requester pro odeslání požadavků DNS na určený server DNS pro rozlišení.  

>[!NOTE]
>Stále budete mít přístup ke konzole správce NSX-T, kde můžete použít upřesňující nastavení uvedená a další funkce NSX-T. 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="Snímek obrazovky zobrazující čtyři možnosti v konzole řešení Azure VMware pro konfiguraci NSX-T.":::

## <a name="prerequisites"></a>Požadavky
Virtuální počítače vytvořené nebo migrované do privátního cloudu řešení Azure VMware by měly být připojené k segmentu. 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>Vytvoření segmentu NSX-T v Azure Portal
Segment NSX-T můžete vytvořit a nakonfigurovat z konzoly řešení Azure VMware v Azure Portal.  Tyto segmenty jsou připojené k výchozí bráně 1 a zatížení na těchto segmentech získají East-West a North-South připojení. Po vytvoření segmentu se zobrazí v NSX-T Manageru a vCenter.

>[!NOTE]
>Pokud plánujete používat protokol DHCP, budete muset před vytvořením a konfigurací segmentu NSX-T [nakonfigurovat server DHCP nebo službu DHCP Relay](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) .

1. V privátním cloudu řešení Azure VMware v části **zatížení sítě** vyberte **segmenty**  >  **Přidat**. Zadejte podrobnosti nového logického segmentu a vyberte **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="Snímek obrazovky ukazující, jak přidat nový segment":::
   
   - **Název segmentu** – název logického přepínače, který je viditelný v vCenter.
   - **Brána podsítě** – IP adresa brány pro podsíť logického přepínače s maskou podsítě. Virtuální počítače jsou připojené k logickému přepínači a všechny virtuální počítače, které se připojují k tomuto přepínači, patří do stejné podsítě.  Všechny virtuální počítače připojené k tomuto logickému segmentu navíc musí mít IP adresu ze stejného segmentu.
   - **DHCP** (volitelné) – rozsahy DHCP pro logický segment. [Server DHCP nebo předávání DHCP](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) musí být nakonfigurovány tak, aby na segmentech využil protokol DHCP.  
   - **Připojená brána**  -  *Vybrané ve výchozím nastavení a jen pro čtení.*  Brána a typ informací o segmentech úrovně 1. 
      - **T1** – název brány vrstvy 1 ve Správci NSX-T. Privátní cloud řešení Azure VMware se dodává s bránou NSX-T úrovně 0 v aktivním/aktivním režimu a výchozí bránou NSX-T úrovně 1 v aktivním nebo pohotovostním režimu.  Segmenty vytvořené prostřednictvím konzoly řešení Azure VMware se připojují pouze k výchozí bráně 1 a zatížení těchto segmentů získá East-West a North-South připojení. Pomocí Správce NSX-T můžete vytvořit jenom další brány úrovně 1. Brány vrstvy 1 vytvořené z konzoly správce NSX-T nejsou viditelné v konzole řešení Azure VMware. 
      - Segment překryvných **typů** podporovaný řešením Azure VMware.

   Segment je teď viditelný v konzole řešení Azure VMware, NSX-T a vCenter.

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>Vytvoření serveru DHCP nebo přenosu DHCP v Azure Portal
Můžete vytvořit server DHCP nebo Relay přímo z konzoly řešení Azure VMware v Azure Portal. Server DHCP nebo Relay se připojí k bráně úrovně 1, která se vytvoří při nasazení řešení Azure VMware. Všechny segmenty, ve kterých jste uvedli rozsahy DHCP, budou součástí tohoto protokolu DHCP.  Po vytvoření serveru DHCP nebo přenosu DHCP je nutné definovat podsíť nebo rozsah na úrovni segmentů, aby je bylo možné spotřebovat.

1. V privátním cloudu řešení Azure VMware v části **zatížení sítě** vyberte **DHCP**  >  **Přidat**.

2. Vyberte možnost **Server DHCP** nebo **přenos DHCP** a potom zadejte název serveru nebo předávací a tři IP adresy. 

   >[!NOTE]
   >U protokolu DHCP Relay se pro úspěšnou konfiguraci vyžaduje jenom jedna IP adresa.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="Snímek obrazovky ukazující postup přidání serveru DHCP nebo přenosu DHCP do řešení Azure VMware.":::

4. Dokončete konfiguraci DHCP tak, že zadáte [rozsahy DHCP na logických segmentech](#create-an-nsx-t-segment-in-the-azure-portal) a pak vyberete **OK**.
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>Konfigurace zrcadlení portů v Azure Portal
Zrcadlení portů můžete nakonfigurovat pro monitorování síťového provozu, který zahrnuje předávání kopie každého paketu z jednoho síťového přepínače na jiný. Tato možnost umístí analyzátor protokolu na port, který přijímá zrcadlená data. Analyzuje provoz ze zdroje, virtuálního počítače nebo skupiny virtuálních počítačů a pak se pošle do definovaného cíle. 

Pokud chcete nastavit zrcadlení portů v konzole řešení Azure VMware, postupujte takto:

* [Krok 1. Vytvoření zdrojového a cílového virtuálního počítače nebo skupin virtuálních počítačů](#step-1-create-source-and-destination-vms-or-vm-groups) – zdrojová skupina má jeden virtuální počítač nebo několik virtuálních počítačů, ve kterých je provoz zrcadlený.

* [Krok 2. Vytvořit profil zrcadlení portů](#step-2-create-a-port-mirroring-profile) – můžete definovat směr přenosu pro zdrojové a cílové skupiny virtuálních počítačů.

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>Krok 1. Vytvoření zdrojového a cílového virtuálního počítače nebo skupin virtuálních počítačů

V tomto kroku vytvoříte zdrojovou skupinu virtuálních počítačů a cílovou skupinu virtuálních počítačů.

1. V privátním cloudu řešení Azure VMware v části **zatížení sítě** vyberte   >  **skupiny virtuálních počítačů** zrcadlení portů  >  **Přidat**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="Snímek obrazovky ukazující, jak vytvořit skupinu virtuálních počítačů pro zrcadlení portů.":::

1. Zadejte název nové skupiny virtuálních počítačů, v seznamu vyberte požadované virtuální počítače a pak klikněte na **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="Snímek obrazovky zobrazující seznam virtuálních počítačů, které se mají přidat do skupiny virtuálních počítačů.":::

1. Zopakováním těchto kroků vytvořte cílovou skupinu virtuálních počítačů.

### <a name="step-2-create-a-port-mirroring-profile"></a>Krok 2. Vytvořit profil zrcadlení portů

V tomto kroku definujete profil pro směr provozu zdrojové a cílové skupiny virtuálních počítačů.

>[!NOTE]
>Ujistěte se, že máte vytvořené zdrojové i cílové skupiny virtuálních počítačů.

1. Vyberte **zrcadlení portů**  >  **Přidat** a potom zadejte:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="Snímek obrazovky zobrazující informace požadované pro profil zrcadlení portů":::

   - **Název zrcadlení portů** – popisný název profilu.
   - **Směr** – vyberte si z příchozího, odchozího nebo obousměrného přenosu.
   - **Zdroj** – vyberte zdrojovou skupinu virtuálních počítačů.
   - **Cíl** – vyberte cílovou skupinu virtuálních počítačů.
   - **Popis** – zadejte popis zrcadlení portů.

1. Kliknutím na **OK** dokončete profil. 

   Profil a skupiny virtuálních počítačů se zobrazí v konzole řešení Azure VMware.

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Konfigurace služby DNS pro přeposílání v Azure Portal
Nakonfigurujete server DNS pro přeposílání, kde konkrétní požadavky DNS se předají do určeného serveru DNS pro rozlišení.  Služba DNS pro přeposílání je přidružená k **Výchozí zóně DNS** a až ke třem **zónám plně kvalifikovaného názvu domény**.

>[!TIP]
>[K nakonfigurování služby DNS pro přeposílání můžete použít také konzolu správce NSX-T](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html).

Pokud chcete nastavit službu DNS pro překládání v konzole řešení Azure VMware, postupujte takto:

* [Krok 1. Konfigurace výchozí zóny DNS a plně kvalifikovaného názvu](#step-1-configure-a-default-dns-zone-and-fqdn-zone) domény – při přijetí dotazu DNS porovná server DNS název domény s názvy domén v zóně DNS plně kvalifikovaného názvu domény. 

* [Krok 2. Konfigurace služby DNS](#step-2-configure-dns-service) – nakonfigurujete službu DNS pro přeposílání.

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>Krok 1. Konfigurace výchozí zóny DNS a plně kvalifikovaného názvu domény
Nakonfigurujete výchozí zónu DNS a zónu plně kvalifikovaného názvu domény tak, aby odesílala dotazy DNS na nadřazený server.  Když se přijme dotaz DNS, služba DNS pro překládá název domény v dotazu se stejnými názvy domén DNS zóny DNS. Pokud se najde shoda, dotaz se přepošle na servery DNS zadané v zóně DNS plně kvalifikovaného názvu domény. Pokud se nenajde žádná shoda, dotaz se přepošle na servery DNS zadané ve výchozí zóně DNS.

>[!NOTE]
>Před konfigurací zóny plně kvalifikovaného názvu domény musí být definovaná výchozí zóna DNS. 

1. V privátním cloudu řešení Azure VMware v části **zatížení sítě** vyberte   >  **zóny DNS** DNS  >  **Přidat**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="Snímek obrazovky ukazující, jak přidat zóny DNS a službu DNS":::

1. Vyberte **výchozí ZÓNU DNS** a zadejte:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="Snímek obrazovky ukazující, jak přidat výchozí zónu DNS":::

   1. Název zóny DNS.

   1. Až tři IP adresy serveru DNS ve formátu **8.8.8.8**.

1. Vyberte **zónu plně kvalifikovaného názvu domény** a zadejte:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="Snímek obrazovky ukazující, jak přidat zónu plně kvalifikovaného názvu domény ":::

   1. Název zóny DNS.

   1. Doména plně kvalifikovaného názvu domény.

   1. Až tři IP adresy serveru DNS ve formátu **8.8.8.8**.

1. Výběrem **OK** dokončete přidávání výchozí zóny DNS a služby DNS.

### <a name="step-2-configure-dns-service"></a>Krok 2. Konfigurace služby DNS

1. Vyberte kartu **Služba DNS** a pak vyberte **Přidat**. Zadejte podrobnosti a vyberte **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="Snímek obrazovky zobrazující informace požadované pro službu DNS":::

   >[!TIP]
   >Ve výchozím nastavení je vybraná **Brána-1** a při nasazování řešení Azure VMware se odráží brána vytvořená.

   Služba DNS se úspěšně přidala.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="Snímek obrazovky, na kterém se úspěšně přidala služba DNS":::

