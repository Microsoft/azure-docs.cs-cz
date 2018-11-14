---
title: Kurz – Vytvoření záznamu aliasu Azure DNS pro podporu vrcholů názvů domén ve službě Traffic Manager
description: V tomto kurzu se dozvíte, jak nakonfigurovat záznam aliasu Azure DNS pro podporu používání vrcholu názvu domény ve službě Traffic Manager.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: fc56fe3a5bdfa0f5e1ef4bc309932cb7f57cf27d
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978089"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Kurz: Konfigurace záznamu aliasu pro podporu vrcholů názvů domén ve službě Traffic Manager 

Pro vrchol názvu svojí domény můžete vytvořit záznam aliasu, který bude odkazovat na profil služby Azure Traffic Manager. Příklad: contoso.com. Místo použití služby přesměrování nakonfigurujete službu Azure DNS tak, aby přímo z vaší zóny odkazovala na profil služby Traffic Manager. 


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače hostitele a síťové infrastruktury
> * Vytvoření profilu služby Traffic Manager
> * Vytvoření záznamu aliasu
> * Test záznamu aliasu


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Musíte mít k dispozici název domény, kterou můžete hostovat v Azure DNS a použít k testování. Musíte mít úplnou kontrolu nad touto doménou. Úplná kontrola zahrnuje možnost nastavit pro doménu záznamy názvového serveru (NS).

Pokyny k hostování domény v Azure DNS najdete v [kurzu hostování domény v Azure DNS](dns-delegate-domain-azure-dns.md).

Ukázková doména použitá v tomto kurzu je contoso.com, ale použijte vlastní název domény.

## <a name="create-the-network-infrastructure"></a>Vytvoření síťové infrastruktury
Nejprve vytvořte virtuální síť a podsíť, do které umístíte webové servery.
1. Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.
2. V levém horním rohu portálu vyberte **Vytvořit prostředek**. Do vyhledávacího pole zadejte *skupina prostředků* a vytvořte skupinu prostředků **RG-DNS-Alias-TM**.
3. Vyberte **Vytvořit prostředek** > **Sítě** > **Virtuální síť**.
4. Vytvořte virtuální síť **VNet-Servers**. Umístěte ji do skupiny prostředků **RG-DNS-Alias-TM** a podsíť pojmenujte **SN-Web**.

## <a name="create-two-web-server-virtual-machines"></a>Vytvoření dvou virtuálních počítačů s webovým serverem
1. Vyberte **Vytvořit prostředek** > **Virtuální počítač s Windows Serverem 2016**.
2. Jako název zadejte **Web-01** a umístěte virtuální počítač do skupiny prostředků **RG-DNS-Alias-TM**. Zadejte uživatelské jméno a heslo a vyberte **OK**.
3. Jako **Velikost** vyberte skladovou položku s 8 GB paměti RAM.
4. V části **Nastavení** vyberte virtuální síť **VNet-Servers** a podsíť **SN-Web**.
5. Vyberte **Veřejná IP adresa**. V části **Přiřazení** vyberte **Statické** a pak vyberte **OK**.
6. Jako veřejné příchozí porty vyberte **HTTP** > **HTTPS** > **RDP (3389)** a pak vyberte **OK**.
7. Na stránce **Souhrn** vyberte **Vytvořit**. Dokončení tohoto postupu trvá několik minut.

Celý postup zopakujte a vytvořte další virtuální počítač **Web-02**.

### <a name="add-a-dns-label"></a>Přidání názvu DNS
Veřejné IP adresy potřebují název DNS, aby fungovaly se službou Traffic Manager.
1. Ve skupině prostředků **RG-DNS-Alias-TM** vyberte veřejnou IP adresu **Web-01-ip**.
2. V části **Nastavení** vyberte **Konfigurace**.
3. Do textového pole Popisek názvu DNS zadejte **web01pip**.
4. Vyberte **Uložit**.

Celý postup zopakujte pro veřejnou IP adresu **Web-02-ip**, ale jako popisek názvu DNS použijte **web02pip**.

### <a name="install-iis"></a>Instalace služby IIS

Na virtuální počítač **Web-01** i **Web-02** nainstalujte službu IIS.

1. Připojte se k virtuálnímu počítači **Web-01** a přihlaste se.
2. Na řídicím panelu **Správce serveru** vyberte **Přidat role a funkce**.
3. Třikrát vyberte **Další**. Na stránce **Role serveru** vyberte **Webový server (IIS)**.
4. Vyberte **Přidat funkce** a pak **Další**.
5. Čtyřikrát vyberte **Další**. Pak vyberte **Nainstalovat**. Dokončení tohoto postupu trvá několik minut.
6. Po dokončení instalace vyberte **Zavřít**.
7. Otevřete webový prohlížeč. Přejděte na adresu **localhost** a ověřte, že se zobrazí výchozí webová stránka služby IIS.

Celý postup zopakujte a nainstalujte službu IIS na virtuální počítač **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

1. Otevřete skupinu prostředků **RG-DNS-Alias-TM** a vyberte veřejnou IP adresu **Web-01-ip**. Poznamenejte si IP adresu pro pozdější použití. Zopakujte tento krok pro veřejnou IP adresu **Web-02-ip**.
1. Vyberte **Vytvořit prostředek** > **Sítě** > **Profil služby Traffic Manager**.
2. Jako název zadejte **TM-alias-test**. Umístěte ho do skupiny prostředků **RG-DNS-Alias-TM**.
3. Vyberte **Vytvořit**.
4. Po dokončení nasazení vyberte **Přejít k prostředku**.
5. Na stránce profilu služby Traffic Manager v části **Nastavení** vyberte **Koncové body**.
6. Vyberte **Přidat**.
7. Jako **Typ** vyberte **Externí koncový bod** a jako **Název** zadejte **EP-Web01**.
8. Do textového pole **Plně kvalifikovaný název domény nebo IP adresa** zadejte IP adresu **Web-01-ip**, kterou jste si poznamenali dříve.
9. Vyberte stejné **Umístění** jako pro ostatní prostředky a pak vyberte **OK**.

Celý postup zopakujte a přidejte koncový bod **Web-02**, ale použijte IP adresu **Web-02-ip**, kterou jste si poznamenali dříve.

## <a name="create-an-alias-record"></a>Vytvoření záznamu aliasu

Vytvořte záznam aliasu, který odkazuje na profil služby Traffic Manager.

1. Výběrem zóny Azure DNS ji otevřete.
2. Vyberte **Sada záznamů**.
3. Textové pole **Název** ponechte prázdné, aby představovalo vrchol názvu domény. Příklad: contoso.com.
4. Jako **Typ** ponechte záznam **A**.
5. Vyberte zaškrtávací políčko **Sada záznamů aliasů**.
6. Vyberte **Zvolit službu Azure** a vyberte profil služby Traffic Manager **TM-alias-test**.

## <a name="test-the-alias-record"></a>Test záznamu aliasu

1. Ve webovém prohlížeči přejděte na vrchol názvu vaší domény. Příklad: contoso.com. Zobrazí se výchozí webová stránka služby IIS. Zavřete webový prohlížeč.
2. Vypněte virtuální počítač **Web-01**. Několik minut počkejte, než se úplně vypne.
3. Otevřete nový webový prohlížeč a znovu přejděte na vrchol názvu vaší domény.
4. Opět se zobrazí výchozí webová stránka služby IIS, protože služba Traffic Manager situaci zvládla a přesměrovala provoz do virtuálního počítače **Web-02**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky vytvořené pro účely tohoto kurzu nepotřebujete, odstraňte skupinu prostředků **RG-DNS-Alias-TM**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili záznam aliasu umožňující použít vrchol názvu vaší domény k odkazování na profil služby Traffic Manager. Další informace o Azure DNS a webových aplikacích získáte v kurzu o webových aplikacích.

> [!div class="nextstepaction"]
> [Hostování aplikací s vyrovnáváním zatížení ve vrcholu zóny](./dns-alias-appservice.md)
