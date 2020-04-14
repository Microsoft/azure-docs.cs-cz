---
title: Vytvoření koncového bodu Azure CDN | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak vytvořit nový koncový bod sítě doručování obsahu Azure (CDN), včetně upřesňující nastavení.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 0a130a433c68d0d5cc8c26eae4b81ff264eb0ca2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254011"
---
# <a name="create-an-azure-cdn-endpoint"></a>Vytvoření koncového bodu Azure CDN
Tento článek popisuje všechna nastavení pro vytvoření koncového bodu [sítě doručování obsahu Azure (CDN)](cdn-overview.md) v existujícím profilu CDN. Po vytvoření profilu a koncového bodu můžete začít doručovat obsah zákazníkům. Úvodní příručka k vytvoření profilu a koncového bodu najdete v [tématu Úvodní příručka: Vytvoření profilu a koncového bodu Azure CDN](cdn-create-new-endpoint.md).

## <a name="prerequisites"></a>Požadavky
Před vytvořením koncového bodu CDN musíte vytvořit alespoň jeden profil CDN, který může obsahovat jeden nebo více koncových bodů CDN. K uspořádání koncových bodů CDN podle internetové domény, webové aplikace nebo jiných kritérií můžete použít několik profilů. Vzhledem k tomu, že ceny CDN se používají na úrovni profilu CDN, musíte vytvořit více profilů CDN, pokud chcete použít kombinaci cenových úrovní Azure CDN. Informace o vytvoření profilu CDN naleznete [v tématu Vytvoření nového profilu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="create-a-new-cdn-endpoint"></a>Vytvoření nového koncového bodu CDN

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na svůj profil CDN. Je možné, že jste si ho v předchozím kroku připnuli k řídicímu panelu. Pokud ne, najdete ho tak, že vyberete **Všechny služby** a potom vyberete **Profily CDN**. V podokně **Profily CDN** vyberte profil, ke kterému plánujete přidat koncový bod. 
   
    Otevře se podokno Profil CDN.

2. Vyberte **koncový bod**.
   
    ![Koncový bod pro výběr CDN](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    Zobrazí se stránka **Přidání koncového bodu**.
   
    ![Přidání stránky koncového bodu](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. Do pole **Název** zadejte jedinečný název pro nový koncový bod CDN. Tento název se používá pro přístup k prostředkům uložených v mezipaměti na _ \<adrese název koncového bodu _domény>.azureedge.net.

4. Pro **typ původu**zvolte jeden z následujících typů původu: 
   - **Úložiště** pro Azure Storage
   - **Cloudová služba** pro cloudové služby Azure
   - **WebOvá aplikace** pro Azure Web Apps
   - **Vlastní původ** pro jakýkoli jiný veřejně přístupný webový server původu (hostovaný v Azure nebo jinde)

5. V **případě názvu hostitele Originu**vyberte nebo zadejte doménu původního serveru. V rozevírací adrese jsou uvedeny všechny dostupné původní servery typu, který jste zadali v kroku 4. Pokud jste jako typ původu vybrali **vlastní původ,** zadejte doménu vlastního původního serveru.
    
6. V **části Cesta k počátku**zadejte cestu k prostředkům, které chcete uložit do mezipaměti. Chcete-li povolit ukládání prostředků do mezipaměti v doméně zadané v kroku 5, ponechejte toto nastavení prázdné.
    
7. Do pole **Hlavička počátečního hostitele** zadejte hlavičku hostitele, kterou má Azure CDN odeslat spolu s každou žádostí, nebo ponechte výchozí nastavení.
   
   > [!NOTE]
   > Některé typy původu (například Azure Storage a Web Apps) vyžadují, aby se hlavička hostitele shodovala s doménou původu. Pokud nemáte původ, který vyžaduje hlavičku hostitele odlišnou od své domény, je vhodné ponechat výchozí hodnotu.
   > 
    
8. Pro **port Protokol** a **Origin**zadejte protokoly a porty, které mají být používány pro přístup k prostředkům na zdrojovém serveru. Je nutné vybrat alespoň jeden protokol (HTTP nebo HTTPS). Pro přístup k obsahu HTTPS použijte doménu poskytovanou cdn_\<(koncový bod>_.azureedge.net). 
   
   > [!NOTE]
   > Hodnota **portu Origin** určuje pouze port, který koncový bod používá k načtení informací ze původního serveru. Koncový bod jako takový je dostupný jenom koncovým klientům na výchozích portech HTTP a HTTPS (80 a 443), a to bez ohledu na nastavení **Počáteční port**.  
   > 
   > Koncové body v profilech **Azure CDN od Akamai** pro počáteční porty neumožňují použití plného rozsahu portů. Seznam nepovolených portů původu najdete v tématu [Povolené porty původu Azure CDN společnosti Akamai](/previous-versions/azure/mt757337(v=azure.100)).  
   > 
   > Podpora protokolu HTTPS pro vlastní domény Azure CDN není v Azure CDN z produktů **Akamai podporována.** Další informace najdete v tématu [Konfigurace HTTPS pro vlastní doménu Azure CDN](cdn-custom-ssl.md).
    
9. V **části Optimalizované pro**vyberte typ optimalizace, který nejlépe odpovídá scénáři a typu obsahu, který má koncový bod doručit. Další informace naleznete [v tématu Optimalizace Azure CDN pro typ doručování obsahu](cdn-optimization-overview.md).

    Následující nastavení typu optimalizace jsou podporována podle typu profilu:
    - **Azure CDN Standard od Microsoftu** profily:
       - [**Obecné doručování webových stránek**](cdn-optimization-overview.md#general-web-delivery)

    - **Profily Azure CDN Standard od Verizonu** a **Azure CDN Premium od verizonu:**
       - [**Obecné doručování webových stránek**](cdn-optimization-overview.md#general-web-delivery)
       - [**Dynamická akcelerace webu**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - Azure CDN Standard z profilů **Akamai:**
       - [**Obecné doručování webových stránek**](cdn-optimization-overview.md#general-web-delivery)
       - [**Obecné vysílání datových proudů médií**](cdn-optimization-overview.md#general-media-streaming)
       - [**Streamování médií videa na vyžádání**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Velké stahování souborů**](cdn-optimization-overview.md#large-file-download)
       - [**Dynamická akcelerace webu**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Pokud chcete vytvořit nový koncový bod, vyberte **Přidat**.
   
    Koncový bod se po vytvoření zobrazí v seznamu koncových bodů daného profilu.
    
    ![Koncový bod CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Vzhledem k tomu, že rozšíření registrace nějakou dobu trvá, koncový bod není okamžitě dostupný pro použití: 
    - U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
    - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
    - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 90 minut. 
   
    Pokud se pokusíte použít název domény CDN před rozšířením konfigurace koncového bodu na servery bodu pop (Point-of-presence), může se zobrazit stav odpovědi HTTP 404. Pokud už je to několik hodin od vytvoření koncového bodu a stále se vám zobrazuje stav odpovědi 404, [přečtěte si článek Řešení potíží s koncovými body Azure CDN, které vracejí stavový kód 404](cdn-troubleshoot-endpoint.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků
Chcete-li koncový bod odstranit, když už není potřeba, vyberte ho a pak vyberte **Odstranit**. 

## <a name="next-steps"></a>Další kroky
Chcete-li se dozvědět o vlastních doménách, pokračujte v kurzu pro přidání vlastní domény do koncového bodu CDN.

> [!div class="nextstepaction"]
> [Přidání vlastní domény](cdn-map-content-to-custom-domain.md)


