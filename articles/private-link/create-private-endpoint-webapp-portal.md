---
title: Soukromé připojení k webové aplikaci pomocí Azure Private Endpoint
description: Soukromé připojení k webové aplikaci pomocí Azure Private Endpoint
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287811"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Soukromé připojení k webové aplikaci pomocí Azure Private Endpoint (Preview)

Azure Private Endpoint je základní stavební blok pro privátní spojení v Azure. Umožňuje soukromé připojení k webové aplikaci.
V tomto rychlém startu se dozvíte, jak nasadit webovou aplikaci s privátním koncovým bodem a připojit se k této webové aplikaci z virtuálního počítače.

Další informace najdete [v tématu Použití privátní koncové body pro Azure Web App][privatenedpointwebapp].

> [!Note]
>Náhled je k dispozici v oblastech Východní USA a Západní USA 2 pro všechny premiumv2 Windows a Linux Web Apps. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Virtuální síť a virtuální počítač

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k vaší webové aplikaci prostřednictvím soukromého koncového bodu.

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť.

1. Na levé horní straně obrazovky vyberte Vytvořit**virtuální síť** **pro síť prostředků** > **Networking** > nebo ve vyhledávacím poli vyhledejte **virtuální síť.**

1. V **části Vytvořit virtuální síť**zadejte nebo vyberte tyto informace na kartě Základy:

   > [!div class="mx-imgBorder"]
   > ![Vytvořit virtuální síť][1]

1. Klikněte na **"Další: IP adresy >"** a zadejte nebo vyberte tyto informace:

   > [!div class="mx-imgBorder"]
   >![Konfigurace adres IP][2]

1. V části podsítě klikněte na **"+ Přidat podsíť"** a zadejte následující informace a klikněte na **"Přidat"**

   > [!div class="mx-imgBorder"]
   >![Přidat podsíť][3]

1. Klikněte na **"Recenze + vytvořit"**

1. Po schválení ověření klikněte na **tlačítko "Vytvořit"**

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Na levé horní straně obrazovky na webu Azure Portal vyberte Vytvořit**prostředek** >  > Compute**Virtual Machine.** **Create a resource**

1. V části Vytvořit virtuální počítač – základy zadejte nebo vyberte tyto informace:

   > [!div class="mx-imgBorder"]
   >![Základní virtuální počítač][4]

1. Vyberte **možnost Další: Disky.**

   Zachovat výchozí nastavení.

1. Vyberte **možnost Další: Vytváření sítí**, vyberte tyto informace:

   > [!div class="mx-imgBorder"]
   >![Sítě][5]

1. Klikněte na **"Recenze + vytvořit"**

1. Po schválení zprávy ověření klikněte na **tlačítko Vytvořit.**

## <a name="create-your-web-app-and-private-endpoint"></a>Vytvoření webové aplikace a soukromého koncového bodu

V této části vytvoříte soukromou webovou aplikaci pomocí privátního koncového bodu.

> [!Note]
>Funkce Private Endpoint je k dispozici pouze pro sku SKU Premium V2.

### <a name="web-app"></a>Webová aplikace

1. Na levé horní straně obrazovky na portálu Azure vyberte Vytvořit**webovou** > **webovou aplikaci** **pro prostředky.** > 

1. V části Vytvořit webovou aplikaci – základy zadejte nebo vyberte tyto informace:

   > [!div class="mx-imgBorder"]
   >![Základní webová aplikace][6]

1. Vyberte **možnost "Revize + vytvořit"**

1. Po schválení zprávy ověření klikněte na **tlačítko Vytvořit.**

### <a name="create-the-private-endpoint"></a>Vytvoření soukromého koncového bodu

1. Ve vlastnostech Webové aplikace vyberte **Nastavení** > **sítě** a klikněte na **"Konfigurace připojení privátního koncového bodu"**

   > [!div class="mx-imgBorder"]
   >![Síť webových aplikací][7]

1. V průvodci klikněte na **tlačítko +přidat.**

   > [!div class="mx-imgBorder"]
   >![Soukromý koncový bod webové aplikace][8]

1. Vyplňte informace o předplatném, virtuální síti a podsíti a klikněte na **tlačítko "OK"**

   > [!div class="mx-imgBorder"]
   >![Síť webových aplikací][9]

1. Kontrola vytvoření soukromého koncového bodu

   > [!div class="mx-imgBorder"]
   >![Zkontrolovat][10]
   >![konečné zobrazení soukromého koncového bodu][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

1. Na vyhledávacím panelu portálu zadejte **myVm**
1. Vyberte **tlačítko Připojit**. Po výběru tlačítka Připojit se otevře možnost Připojit k virtuálnímu počítači, **vyberte**

   > [!div class="mx-imgBorder"]
   >![Tlačítko PRV][12]

1. Azure vytvoří soubor rdp (Rdp) a stáhne ho do počítače po kliknutí na **stáhnout soubor RDP**

   > [!div class="mx-imgBorder"]
   >![Stáhnout soubor RDP][13]

1. Otevřete soubor Downloaded.rdp.

- Pokud se zobrazí výzva, vyberte Připojit.
- Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

> [!Note]
> Možná budete muset vybrat další volby > Použijte jiný účet, chcete-li zadat pověření, která jste zadali při vytváření virtuálního účtu.

- Vyberte OK.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte Ano nebo Pokračovat.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji a vraťte se na místní plochu.

## <a name="access-web-app-privately-from-the-vm"></a>Přístup k Webové aplikaci soukromě z virtuálního serveru

V této části se připojíte soukromě k webové aplikaci pomocí privátníkoncový bod.

1. Získejte soukromou IP adresu svého soukromého koncového bodu v panelu hledání typu **Private Link**a vyberte Soukromý odkaz.

   > [!div class="mx-imgBorder"]
   >![Privátní propojení][14]

1. V Centru soukromých odkazů vyberte **Soukromé koncové body,** chcete-li zobrazit všechny soukromé koncové body.

   > [!div class="mx-imgBorder"]
   >![Soukromé linkové centrum][15]

1. Vyberte odkaz Soukromý koncový bod s webovou aplikací a podsítí.

   > [!div class="mx-imgBorder"]
   >![Vlastnosti soukromého koncového bodu][16]

1. Zkopírujte privátní IP adresu svého soukromého koncového bodu a hlavní hod-dna webové aplikace, v našem případě webappdemope.azurewebsites.net 10.10.2.4

1. V myVM ověřte, že webová aplikace není přístupná prostřednictvím veřejné IP adresy. Otevřete prohlížeč a vložte název webové aplikace, musíte mít 403 zakázanou chybovou stránku

   > [!div class="mx-imgBorder"]
   >![Forbidden][17]

> [!Important]
> Vzhledem k tomu, že tato funkce je ve verzi Preview, je třeba ručně spravovat položku DNS.

1. Vytvoření položky hostitele, otevření průzkumníka souborů a vyhledání souboru hostitelů

   > [!div class="mx-imgBorder"]
   >![Soubor hosts][18]

1. Přidání položky se soukromou IP adresou a veřejným názvem webové aplikace úpravou souboru hosts pomocí poznámkového bloku

   > [!div class="mx-imgBorder"]
   >![Hostitelé obsahu][19]

1. Soubor uložte

1. Otevření prohlížeče a zadání adresy URL webové aplikace

   > [!div class="mx-imgBorder"]
   >![Webový server s PE][20]

1. K webové aplikaci přistupujete prostřednictvím soukromého koncového bodu

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až skončíte s privátním koncovým bodem, Web Appem a virtuálním virtuálním serverem, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole Hledat v horní části portálu zadejte ready-rg a z výsledků hledání vyberte ready-rg.
1. Vyberte Odstranit skupinu prostředků.
1. Zadejte ready-rg pro typ názvu skupiny prostředků a vyberte Odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač ve virtuální síti, webové aplikaci a soukromém koncovém bodu. Připojili jste se k virtuálnímu virtuálnímu zařízení z Internetu a bezpečně jste je komunikovali s webovou aplikací pomocí privátního odkazu. Další informace o privátním koncovém bodu najdete v [tématu Co je Azure Private Endpoint][privateendpoint].

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
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
