---
title: Kurz – Vytvoření záznamu aliasu Azure DNS odkazujícího na veřejnou IP adresu Azure
description: V tomto kurzu se dozvíte, jak nakonfigurovat záznam aliasu Azure DNS tak, aby odkazoval na veřejnou IP adresu Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 842015d853790e3ac78214cca6a70becb7f9fadf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991217"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Kurz: Konfigurace záznamu aliasu odkazujícího na veřejnou IP adresu Azure 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření síťové infrastruktury
> * Vytvoření virtuálního počítače s webovým serverem
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
2. V levém horním rohu portálu klikněte na **Vytvořit prostředek**, do vyhledávacího pole zadejte *skupina prostředků* a vytvořte skupinu prostředků **RG-DNS-Alias-pip**.
3. Klikněte na **Vytvořit prostředek**, pak na **Sítě** a nakonec na **Virtuální síť**.
4. Vytvořte virtuální síť **VNet-Server**, umístěte ji do skupiny prostředků **RG-DNS-Alias-pip** a podsíť pojmenujte **SN-Web**.

## <a name="create-a-web-server-virtual-machine"></a>Vytvoření virtuálního počítače s webovým serverem
1. Klikněte na **Vytvořit prostředek** a pak na **Virtuální počítač s Windows Serverem 2016**.
2. Jako název zadejte **Web-01** a umístěte virtuální počítač do skupiny prostředků **RG-DNS-Alias-TM**. Zadejte uživatelské jméno a heslo a klikněte na **OK**.
3. Jako **Velikost** zvolte skladovou položku s 8 GB paměti RAM.
4. V části **Nastavení** vyberte virtuální síť **VNet-Servers** a podsíť **SN-Web**. Jako veřejné příchozí porty vyberte **HTTP**, **HTTPS** a **RDP (3389)** a pak klikněte na **OK**.
5. Na stránce Souhrn klikněte na **Vytvořit**.

   Tato akce trvá několik minut.

### <a name="install-iis"></a>Instalace služby IIS

Na virtuální počítač **Web-01** nainstalujte službu IIS.

1. Připojte se k virtuálnímu počítači **Web-01** a přihlaste se.
2. Na řídicím panelu Správce serveru klikněte na **Přidat role a funkce**.
3. Třikrát klikněte na **Další** a na stránce **Role serveru** vyberte **Webový server (IIS)**.
4. Klikněte na **Přidat funkce** a pak na **Další**.
5. Čtyřikrát klikněte na **Další** a pak klikněte na **Nainstalovat**.

   Dokončení tohoto procesu může několik minut trvat.
6. Až instalace skončí, klikněte na **Zavřít**.
7. Otevřete webový prohlížeč, přejděte na adresu **localhost** a ověřte, že se zobrazí výchozí webová stránka služby IIS.

## <a name="create-an-alias-record"></a>Vytvoření záznamu aliasu

Vytvořte záznam aliasu, který odkazuje na veřejnou IP adresu.

1. Kliknutím na zónu Azure DNS ji otevřete.
2. Klikněte na **Sada záznamů**.
3. Do textového pole **Název** zadejte **web01**.
4. Jako **Typ** ponechte záznam **A**.
5. Klikněte na zaškrtávací políčko **Sada záznamů aliasů**.
6. Klikněte na **Zvolit službu Azure** a vyberte veřejnou IP adresu **Web-01-ip**.

## <a name="test-the-alias-record"></a>Test záznamu aliasu

1. Ve skupině prostředků **RG-DNS-Alias-pip** klikněte na virtuální počítač **Web-01**. Poznamenejte si veřejnou IP adresu.
1. Ve webovém prohlížeči přejděte na plně kvalifikovaný název domény virtuálního počítače Web01-01. Příklad: **web01.contoso.com**. Měla by se zobrazit výchozí webová stránka služby IIS.
2. Zavřete webový prohlížeč.
3. Zastavte virtuální počítač **Web-01** a pak ho restartujte.
4. Jakmile se virtuální počítač restartuje, poznamenejte si jeho novou veřejnou IP adresu.
5. Otevřete nový prohlížeč a znovu přejděte na plně kvalifikovaný název domény virtuálního počítače Web01-01. Příklad: **web01.contoso.com**.
6. I teď by vše mělo proběhnout úspěšně, protože jste použili záznam aliasu odkazující na prostředek veřejné IP adresy, a ne standardní záznam A.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nejsou potřeba, můžete všechny prostředky vytvořené pro účely tohoto kurzu odstranit odstraněním skupiny prostředků **RG-DNS-Alias-pip**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili záznam aliasu odkazující na veřejnou IP adresu Azure. Další informace o Azure DNS a webových aplikacích získáte v kurzu o webových aplikacích.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)
