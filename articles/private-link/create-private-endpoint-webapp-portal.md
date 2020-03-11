---
title: Připojení soukromě k webové aplikaci pomocí privátního koncového bodu Azure
description: Připojení soukromě k webové aplikaci pomocí privátního koncového bodu Azure
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: bb78536326885e043279de1ff77e6e8efcd95193
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037154"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Připojení soukromě k webové aplikaci pomocí privátního koncového bodu Azure (Preview)

Privátní koncový bod Azure je základním stavebním blokem privátního propojení v Azure. Umožňuje připojit soukromě k webové aplikaci.
V tomto rychlém startu se dozvíte, jak nasadit webovou aplikaci s privátním koncovým bodem a jak se připojit k této webové aplikaci z virtuálního počítače.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Virtuální síť a virtuální počítač

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k vaší webové aplikaci prostřednictvím privátního koncového bodu.

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek** > **síť** > **virtuální síť** nebo ve vyhledávacím poli vyhledejte **virtuální síť** .

1. V části **vytvořit virtuální síť**zadejte nebo vyberte tyto informace na kartě základy:

 ![Vytvořit Virtual Network][1]

1. Klikněte na **Další: IP adresy >** a zadejte nebo vyberte tyto informace:

![Konfigurace IP adres][2]

1. V části podsíť klikněte na **+ Přidat podsíť** a zadejte následující informace a klikněte na **Přidat** .

![Přidat podsíť][3]

1. Klikněte na **zkontrolovat + vytvořit** .

1. Po úspěšném ověření klikněte na **vytvořit** .

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit prostředek** > **výpočetní** > **virtuální počítač** .

1. V nástroji vytvořit virtuální počítač základy zadejte nebo vyberte tyto informace:

![Základní virtuální počítač ][4]

1. Vyberte **Další: disky.**

Zachovat výchozí nastavení.

1. Vyberte **Další: sítě**, vyberte tyto informace:

![Sítě ][5]

1. Klikněte na **zkontrolovat + vytvořit** .

1. Po úspěšném ověření klikněte na **vytvořit** .

## <a name="create-your-web-app-and-private-endpoint"></a>Vytvoření webové aplikace a privátního koncového bodu

V této části vytvoříte soukromou webovou aplikaci s použitím privátního koncového bodu.

> [!Note]
>Funkce privátního koncového bodu je dostupná jenom pro Premium v2 a izolovaná s externí SKU služby pro pořízení.

### <a name="web-app"></a>Webová aplikace

1. V levém horním rohu obrazovky Azure Portal vyberte **vytvořit prostředek** > webové **aplikace** **webové** > .

1. V nástroji vytvořit webovou aplikaci – základy zadejte nebo vyberte tyto informace:

![Web App Basic ][6]

1. Vyberte **"zkontrolovat + vytvořit"**

1. Po úspěšném ověření klikněte na **vytvořit** .

### <a name="create-the-private-endpoint"></a>Vytvoření privátního koncového bodu

1. Ve vlastnostech webové aplikace vyberte **nastavení** > **sítě** a klikněte na **Konfigurace připojení privátního koncového bodu** .

![Sítě webové aplikace][7]

1. V průvodci klikněte na **+ Přidat** .

![Privátní koncový bod webové aplikace][8]

1. Vyplňte informace o předplatném, virtuální síti a podsíti a klikněte na **OK** .

![Sítě webové aplikace][9]

1. Kontrola vytvoření privátního koncového bodu

![kontrola][10]
![finálního zobrazení privátního koncového bodu][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

1. Na panelu hledání na portálu zadejte **myVm** .
1. Klikněte na **tlačítko připojit**. Po výběru tlačítka připojit se otevře okno připojit k virtuálnímu počítači, vyberte **RDP** .

![Tlačítko RDP][12]

1. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (. RDP) a stáhne ho do počítače po kliknutí na **Stáhnout soubor RDP** .

![Stáhnout soubor RDP][13]

1. Otevřete stažený soubor. RDP.

- Po zobrazení výzvy vyberte připojit.
- Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

> [!Note]
> Možná budete muset vybrat další volby > použít jiný účet a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

- Vyberte OK.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění certifikátu, vyberte Ano nebo pokračovat.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.

## <a name="access-web-app-privately-from-the-vm"></a>Přístup k webové aplikaci soukromě z virtuálního počítače

V této části se soukromě připojíte k webové aplikaci pomocí privátního koncového bodu.

1. Získejte privátní IP adresu privátního koncového bodu, na panelu hledání zadejte **privátní odkaz**a vyberte privátní odkaz.

![Privátní propojení][14]

1. V centru privátních odkazů vyberte **privátní koncové body** pro výpis všech vašich privátních koncových bodů.

![Centrum privátních odkazů][15]

1. Vyberte odkaz privátního koncového bodu do vaší webové aplikace a podsítě.

![Vlastnosti privátního koncového bodu][16]

1. Zkopírujte privátní IP adresu vašeho privátního koncového bodu a plně kvalifikovaný název domény vaší webové aplikace, v našem případě webappdemope.azurewebsites.net 10.10.2.4

1. V myVM ověřte, že webová aplikace není přístupná prostřednictvím veřejné IP adresy. Otevřete prohlížeč a zkopírujte název webové aplikace, musíte mít chybovou stránku 403 zakázáno.

![Forbidden][17]

> [!Note]
> Vzhledem k tomu, že je tato funkce ve verzi Preview, je nutné ručně spravovat položku DNS.

1. Vytvořte položku hostitele, otevřete Průzkumníka souborů a vyhledejte soubor Hosts.

![Soubor hostitelů][18]

1. Úpravou souboru hostitelů pomocí programu Poznámkový blok přidejte položku s privátní IP adresou a veřejným názvem vaší webové aplikace.

![Obsah hostitelů][19]

1. Soubor uložte

1. Otevřete prohlížeč a zadejte adresu URL vaší webové aplikace.

![Web s PE][20]

1. K webové aplikaci přistupujete prostřednictvím privátního koncového bodu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi s použitím privátního koncového bodu, webové aplikace a virtuálního počítače, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do vyhledávacího pole v horní části portálu zadejte připravené – RG a ve výsledcích hledání vyberte připraveno-RG.
1. Vyberte Odstranit skupinu prostředků.
1. Zadejte RG ready pro zadejte název skupiny prostředků a vyberte Odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač ve virtuální síti, webové aplikaci a privátním koncovém bodu. Připojili jste se k virtuálnímu počítači z Internetu a zabezpečeně komunikovali webové aplikaci pomocí privátního odkazu. Další informace o privátním koncovém bodu najdete v tématu [co je privátní koncový bod Azure][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png

<!--Links-->
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
