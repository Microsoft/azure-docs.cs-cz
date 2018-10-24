---
title: Kurz – Vytvoření záznamu aliasu Azure DNS pro podporu vrcholů názvů domén ve službě Traffic Manager
description: V tomto kurzu se dozvíte, jak nakonfigurovat záznam aliasu Azure DNS pro podporu používání vrcholu názvu domény ve službě Traffic Manager.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 4a5d8968861f6f2938e605d2f7106529ca401df4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967434"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Kurz: Konfigurace záznamu aliasu pro podporu vrcholů názvů domén ve službě Traffic Manager 

Pro vrchol názvu svojí domény (například contoso.com) můžete vytvořit záznam aliasu, který bude odkazovat na profil služby Traffic Manager. Takže místo toho, abyste k tomu použili službu přesměrování, můžete nakonfigurovat službu Azure DNS tak, aby přímo z vaší zóny odkazovala na profil služby Traffic Manager. 


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače hostitele a síťové infrastruktury
> * Vytvoření profilu Traffic Manageru
> * Vytvoření záznamu aliasu
> * Test záznamu aliasu


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Musíte mít k dispozici název domény, kterou můžete hostovat v Azure DNS a použít k testování. Nad touto doménou musíte mít úplnou kontrolu, včetně možnosti nastavit pro ni záznamy názvového serveru (NS).

Pokyny k hostování domény v Azure DNS najdete v [kurzu hostování domény v Azure DNS](dns-delegate-domain-azure-dns.md).

Ukázková doména použitá v tomto kurzu je contoso.com, ale měli byste použít vlastní název domény.

## <a name="create-the-network-infrastructure"></a>Vytvoření síťové infrastruktury
Nejprve vytvořte virtuální síť a podsíť, do které umístíte webové servery.
1. Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.
2. V levém horním rohu portálu klikněte na **Vytvořit prostředek**, do vyhledávacího pole zadejte *skupina prostředků* a vytvořte skupinu prostředků **RG-DNS-Alias-TM**.
3. Klikněte na **Vytvořit prostředek**, pak na **Sítě** a nakonec na **Virtuální síť**.
4. Vytvořte virtuální síť **VNet-Servers**, umístěte ji do skupiny prostředků **RG-DNS-Alias-TM** a podsíť pojmenujte **SN-Web**.

## <a name="create-two-web-server-virtual-machines"></a>Vytvoření dvou virtuálních počítačů s webovým serverem
1. Klikněte na **Vytvořit prostředek** a pak na **Virtuální počítač s Windows Serverem 2016**.
2. Jako název zadejte **Web-01** a umístěte virtuální počítač do skupiny prostředků **RG-DNS-Alias-TM**. Zadejte uživatelské jméno a heslo a klikněte na **OK**.
3. Jako **Velikost** zvolte skladovou položku s 8 GB paměti RAM.
4. V části **Nastavení** vyberte virtuální síť **VNet-Servers** a podsíť **SN-Web**.
5. Klikněte na **Veřejná IP adresa**, v části **Přiřazení** klikněte na **Statická** a pak klikněte na **OK**.
6. Jako veřejné příchozí porty vyberte **HTTP**, **HTTPS** a **RDP (3389)** a pak klikněte na **OK**.
7. Na stránce Souhrn klikněte na **Vytvořit**.

   Tato akce trvá několik minut.
6. Celý postup zopakujte a vytvořte další virtuální počítač **Web-02**.

### <a name="add-a-dns-label"></a>Přidání názvu DNS
Veřejné IP adresy potřebují název DNS, aby fungovaly se službou Traffic Manager.
1. Ve skupině prostředků **RG-DNS-Alias-TM** klikněte na veřejnou IP adresu **Web-01-ip**.
2. V části **Nastavení** klikněte na **Konfigurace**.
3. Do textového pole Popisek názvu DNS zadejte **web01pip**.
4. Klikněte na **Uložit**.

Celý postup zopakujte pro veřejnou IP adresu **Web-02-ip**, ale jako popisek názvu DNS použijte **web02pip**.

### <a name="install-iis"></a>Instalace služby IIS

Na virtuální počítač **Web-01** i **Web-02** nainstalujte službu IIS.

1. Připojte se k virtuálnímu počítači **Web-01** a přihlaste se.
2. Na řídicím panelu Správce serveru klikněte na **Přidat role a funkce**.
3. Třikrát klikněte na **Další** a na stránce **Role serveru** vyberte **Webový server (IIS)**.
4. Klikněte na **Přidat funkce** a pak na **Další**.
5. Čtyřikrát klikněte na **Další** a pak klikněte na **Nainstalovat**.

   Dokončení tohoto procesu může několik minut trvat.
6. Až instalace skončí, klikněte na **Zavřít**.
7. Otevřete webový prohlížeč, přejděte na adresu **localhost** a ověřte, že se zobrazí výchozí webová stránka služby IIS.

Celý postup zopakujte a nainstalujte službu IIS na virtuální počítač **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Doplnit 

1. Otevřete skupinu prostředků **RG-DNS-Alias-TM** a klikněte na veřejnou IP adresu **Web-01-ip**. Poznamenejte si IP adresu pro pozdější použití. Celý postup zopakujte pro veřejnou IP adresu **Web-02-ip**.
1. Klikněte na **Vytvořit prostředek**, pak na **Sítě** a nakonec na **Profil služby Traffic Manager**.
2. Jako název zadejte **TM-alias-test** a umístěte profil do skupiny prostředků **RG-DNS-Alias-TM**.
3. Klikněte na možnost **Vytvořit**.
4. Po dokončení nasazení klikněte na **Přejít k prostředku**.
5. Na stránce profilu služby Traffic Manager v části **Nastavení** klikněte na **Koncové body**.
6. Klikněte na tlačítko **Add** (Přidat).
7. Jako **Typ** vyberte **Externí koncový bod** a jako **Název** zadejte **EP-Web01**.
8. Do textového pole **Plně kvalifikovaný název domény nebo IP adresa** zadejte IP adresu **Web-01-ip**, kterou jste si poznamenali dříve.
9. Vyberte stejné **Umístění** jako pro ostatní prostředky a pak klikněte na **OK**.

Celý postup zopakujte a přidejte koncový bod **Web-02**, ale použijte IP adresu **Web-02-ip**, kterou jste si poznamenali dříve.

## <a name="create-an-alias-record"></a>Vytvoření záznamu aliasu

Vytvořte záznam aliasu, který odkazuje na profil služby Traffic Manager.

1. Kliknutím na zónu Azure DNS ji otevřete.
2. Klikněte na **Sada záznamů**.
3. Textové pole **Název** ponechte prázdné, aby představovalo vrchol názvu domény (například contoso.com).
4. Jako **Typ** ponechte záznam **A**.
5. Klikněte na zaškrtávací políčko **Sada záznamů aliasů**.
6. Klikněte na **Zvolit službu Azure** a vyberte profil služby Traffic Manager **TM-alias-test**.

## <a name="test-the-alias-record"></a>Test záznamu aliasu

1. Ve webovém prohlížeči přejděte na vrchol názvu vaší domény (například contoso.com). Měla by se zobrazit výchozí webová stránka služby IIS. Zavřete webový prohlížeč.
2. Vypněte virtuální počítač **Web-01** a několik minut počkejte, než se úplně nevypne.
3. Otevřete nový webový prohlížeč a znovu přejděte na vrchol názvu vaší domény.
4. Opět by se měla zobrazit výchozí stránka služby IIS, protože služba Traffic Manager situaci zvládla a přesměrovala provoz do virtuálního počítače **Web-02**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nejsou potřeba, můžete všechny prostředky vytvořené pro účely tohoto kurzu odstranit odstraněním skupiny prostředků **RG-DNS-Alias-TM**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili záznam aliasu umožňující použít vrchol názvu vaší domény k odkazování na profil služby Traffic Manager. Další informace o Azure DNS a webových aplikacích získáte v kurzu o webových aplikacích.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)
