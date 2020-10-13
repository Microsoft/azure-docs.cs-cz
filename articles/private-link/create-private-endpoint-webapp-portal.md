---
title: Připojení soukromě k webové aplikaci pomocí privátního koncového bodu Azure
description: Tento článek vysvětluje, jak soukromě připojit k webové aplikaci pomocí privátního koncového bodu Azure.
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 10/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3e0f05240aba9b5c92689315e409aaabe793b3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91772834"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint"></a>Připojení soukromě k webové aplikaci pomocí privátního koncového bodu Azure

Soukromý koncový bod Azure je základní stavební blok pro privátní propojení Azure. Pomocí privátního koncového bodu se můžete soukromě připojit k webové aplikaci. V tomto článku se dozvíte, jak nasadit webovou aplikaci pomocí privátního koncového bodu a jak se připojit k webové aplikaci z virtuálního počítače.

Další informace najdete v tématu [použití privátních koncových bodů pro webovou aplikaci Azure][privateendpointwebapp].

> [!Note]
> Privátní koncový bod je k dispozici ve veřejných oblastech pro PremiumV2 webové aplikace PremiumV3 Windows, Linux Web Apps a plán služby Azure Functions Premium (někdy se mu říká plán elastické Premium). 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Než začnete, přihlaste se k [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network-and-virtual-machine"></a>Vytvoření virtuální sítě a virtuálního počítače

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který budete používat pro přístup k webové aplikaci prostřednictvím privátního koncového bodu.

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Pokud chcete vytvořit virtuální síť a podsíť, udělejte toto:

1. V levém podokně vyberte **vytvořit prostředek**  >  **síť**  >  **virtuální síť**.

1. V podokně **vytvořit virtuální síť** vyberte kartu **základy** a pak zadejte informace, které jsou tady uvedené:

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s podoknem vytvořit Virtual Network v Azure Portal][1]

1. Vyberte kartu **IP adresy** a potom zadejte informace, které jsou tady uvedené:

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky karty IP adresy v podokně vytvořit virtuální síť.][2]

1. V části **podsíť** vyberte **Přidat podsíť**, zadejte informace, které jsou tady uvedené, a pak vyberte **Přidat**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s podoknem přidat podsíť][3]

1. Vyberte **Zkontrolovat a vytvořit**.

1. Po úspěšném ověření vyberte **vytvořit**.

### <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

Pokud chcete vytvořit virtuální počítač, udělejte toto:

1. V Azure Portal v levém podokně vyberte **vytvořit prostředek**  >  **Compute**  >  **virtuální počítač**Compute.

1. V podokně **vytvořit základní informace o virtuálním počítači** zadejte informace, které jsou tady uvedené:

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s podoknem vytvořit virtuální počítač][4]

1. Vyberte **Další: disky**.

1. V podokně **disky** ponechte výchozí nastavení a potom vyberte **Další: sítě**.

1. V podokně **síť** zadejte informace, které jsou zde zobrazeny:

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s kartou "sítě" v podokně vytvořit virtuální počítač.][5]

1. Vyberte **Zkontrolovat a vytvořit**.

1. Po úspěšném ověření vyberte **vytvořit**.

## <a name="create-a-web-app-and-a-private-endpoint"></a>Vytvoření webové aplikace a privátního koncového bodu

V této části vytvoříte soukromou webovou aplikaci, která používá privátní koncový bod.

> [!Note]
> Funkce privátního koncového bodu je k dispozici pouze pro vrstvu PremiumV2 a PremiumV3.

### <a name="create-the-web-app"></a>Vytvoření webové aplikace

1. V Azure Portal v levém podokně vyberte **vytvořit prostředek**  >  **Webová**  >  **Webová aplikace**.

1. V podokně **Webová aplikace** vyberte kartu **základy** a pak zadejte informace, které jsou tady uvedené:

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s kartou základy v podokně webová aplikace][6]

1. Vyberte **Zkontrolovat a vytvořit**.

1. Po úspěšném ověření vyberte **vytvořit**.

### <a name="create-the-private-endpoint"></a>Vytvoření privátního koncového bodu

1. Ve vlastnostech webové aplikace v části **Nastavení**vyberte **sítě**a potom v části * * připojení privátních koncových bodů * * vyberte **Konfigurovat připojení privátních koncových bodů**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s odkazem konfigurace připojení privátního koncového bodu v podokně sítě webové aplikace][7]

1. V průvodci **připojením privátního koncového bodu** vyberte **Přidat**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s tlačítkem Přidat v průvodci připojení privátních koncových bodů][8]

1. V rozevíracím seznamu **odběr**, **virtuální síť**a **podsíť** vyberte správné informace a pak vyberte **OK**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s podoknem přidat privátní koncový bod][9]

1. Monitorujte průběh vytváření privátního koncového bodu.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky průběhu přidávání privátního koncového bodu ][10]
   >  ![ Snímek obrazovky nově vytvořeného privátního koncového bodu.][11]

## <a name="connect-to-the-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z Internetu

1. Do **vyhledávacího** pole Azure Portal zadejte **myVm**.
1. Vyberte **připojit**a pak vyberte **RDP**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s tlačítkem "RDP" v podokně "myVM".][12]

1. V podokně **připojit se pomocí protokolu RDP** vyberte **Stáhnout soubor RDP**.  

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s tlačítkem "Stáhnout soubor RDP" v podokně "připojit k protokolu RDP".][13]

   Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (RDP) a stáhne ho do vašeho počítače.   

1. Otevřete stažený soubor RDP.

   a. Na příkazovém řádku vyberte **připojit**.  
   b. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

     > [!Note]
     > Chcete-li použít tyto přihlašovací údaje, možná budete muset vybrat **Další možnosti**  >  **použít jiný účet**.

1. Vyberte **OK**.

   > [!Note]
   > Pokud během procesu přihlašování dojde k upozornění na certifikát, vyberte **Ano** nebo **pokračovat**.

1. Když se zobrazí okno plocha virtuálního počítače, minimalizujte ho a vraťte se zpátky na místní plochu.

## <a name="access-the-web-app-privately-from-the-vm"></a>Přístup k webové aplikaci soukromě z virtuálního počítače

V této části se soukromě připojíte k webové aplikaci pomocí privátního koncového bodu.

1. Pokud chcete získat soukromou IP adresu privátního koncového bodu, zadejte do **vyhledávacího** pole **privátní odkaz** a potom v seznamu výsledků vyberte **privátní odkaz**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s odkazem "soukromé propojení" v seznamu výsledků hledání.][14]

1. V centru privátních odkazů v levém podokně vyberte **privátní koncové body** , aby se zobrazily vaše soukromé koncové body.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky se seznamem privátních koncových bodů v centru privátních odkazů][15]

1. Vyberte privátní koncový bod, který odkazuje na vaši webovou aplikaci a vaši podsíť.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s podoknem vlastností privátního koncového bodu][16]

1. Zkopírujte soukromou IP adresu vašeho privátního koncového bodu a plně kvalifikovaný název domény (FQDN) vaší webové aplikace. V předchozím příkladu je soukromé ID *`webappdemope.azurewebsites.net 10.10.2.4`* .

1. V podokně **myVM** ověřte, že webová aplikace je nepřístupná prostřednictvím veřejné IP adresy. Provedete to tak, že otevřete prohlížeč a vložíte název webové aplikace. Na stránce by se měla zobrazit zpráva "Chyba 403 – zakázáno".

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s chybovou stránkou Error 403-Forbidden][17]

   V případě DNS proveďte jednu z následujících akcí:
 
   - Použijte službu Azure DNS privátní zóny.  

     a. Vytvořte soukromou zónu DNS s názvem *`privatelink.azurewebsites.net`* a pak ji propojte s virtuální sítí.  
     b. Vytvořte dva záznamy A (tj. název aplikace a název správce řízení služeb [SCM]) s IP adresou vašeho privátního koncového bodu.  
     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky záznamů privátní zóny DNS][21]  

   - Použijte soubor *hostitelů* virtuálního počítače.  

     a. Vytvořte položku hostitelé, otevřete Průzkumníka souborů a vyhledejte soubor *hosts* .  
     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky zobrazující soubor hostitelů v Průzkumníkovi souborů.][18]  
     b. Úpravou souboru *hosts* v textovém editoru přidejte položku, která obsahuje soukromou IP adresu a veřejný název vaší webové aplikace.  
     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky s textem souboru hostitelů][19]  
     c. Uložte soubor.

1. V prohlížeči zadejte adresu URL vaší webové aplikace.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky prohlížeče zobrazujícího webovou aplikaci][20]

Nyní přistupujete k webové aplikaci prostřednictvím privátního koncového bodu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi s použitím privátního koncového bodu, webové aplikace a virtuálního počítače, odstraňte skupinu prostředků a všechny prostředky, které obsahuje.

1. V Azure Portal do **vyhledávacího** pole zadejte **RG**a potom v seznamu výsledků vyberte **připraveno-RG** .

1. Vyberte **Odstranit skupinu prostředků**.

1. V části **Zadejte název skupiny prostředků**zadejte **připravený – RG**a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili virtuální počítač ve virtuální síti, webové aplikaci a privátním koncovém bodu. Připojili jste se k virtuálnímu počítači z Internetu a zabezpečeně komunikovali webové aplikaci pomocí privátního odkazu. 

Další informace o privátním koncovém bodu najdete v tématu [co je privátní koncový bod Azure][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
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
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
