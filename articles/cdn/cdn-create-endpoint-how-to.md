---
title: Vytvoření koncového bodu Azure CDN | Microsoft Docs
description: Tento článek ukazuje, jak vytvořit nový koncový bod Azure Content Delivery Network (CDN), včetně rozšířených nastavení.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/12/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 43718f8ebc851f27035f2999bfb4ff3ec12ca5b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84887731"
---
# <a name="create-an-azure-cdn-endpoint"></a>Vytvoření koncového bodu Azure CDN
Tento článek popisuje všechna nastavení pro vytvoření koncového bodu služby [Azure Content Delivery Network (CDN)](cdn-overview.md) v existujícím profilu CDN. Po vytvoření profilu a koncového bodu můžete začít doručování obsahu vašim zákazníkům. Rychlý Start při vytváření profilu a koncového bodu najdete v tématu [rychlý Start: vytvoření profilu Azure CDN a koncového bodu](cdn-create-new-endpoint.md).

## <a name="prerequisites"></a>Požadavky
Než budete moct vytvořit koncový bod CDN, musíte vytvořit aspoň jeden profil CDN, který může obsahovat jeden nebo víc koncových bodů CDN. K uspořádání koncových bodů CDN podle internetové domény, webové aplikace nebo jiných kritérií můžete použít několik profilů. Vzhledem k tomu, že ceny CDN se používají na úrovni profilu CDN, musíte vytvořit několik profilů CDN, pokud chcete používat kombinaci Azure CDN cenové úrovně. Pokud chcete vytvořit profil CDN, přečtěte si téma [Vytvoření nového profilu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="create-a-new-cdn-endpoint"></a>Vytvoření nového koncového bodu CDN

1. V [Azure Portal](https://portal.azure.com)přejděte do svého profilu CDN. Je možné, že jste si ho v předchozím kroku připnuli k řídicímu panelu. Pokud ne, najdete ho tak, že vyberete **Všechny služby** a potom vyberete **Profily CDN**. V podokně **Profily CDN** vyberte profil, ke kterému plánujete přidat koncový bod. 
   
    Otevře se podokno Profil CDN.

2. Vyberte **koncový bod**.
   
    ![Vybrat koncový bod CDN](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    Zobrazí se stránka **Přidání koncového bodu**.
   
    ![Přidat stránku koncového bodu](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. Do pole **Název** zadejte jedinečný název pro nový koncový bod CDN. Tento název se používá pro přístup k prostředkům v mezipaměti v doméně _\<endpointname>_ . azureedge.NET.

4. Jako **Typ původu**vyberte jeden z následujících typů původu: 
   - **Úložiště** pro Azure Storage
   - **Cloudová služba** pro Azure Cloud Services
   - **Webová aplikace** pro Azure Web Apps
   - **Vlastní původ** pro jakýkoliv jiný veřejně přístupný zdrojový webový server (hostovaný v Azure nebo jinde)

5. V případě **zdrojového názvu hostitele**vyberte nebo zadejte doménu zdrojového serveru. Rozevírací seznam obsahuje všechny dostupné zdrojové servery typu, který jste zadali v kroku 4. Pokud jste jako typ zdroje vybrali možnost **vlastní zdroj** , zadejte doménu svého vlastního zdrojového serveru.
    
6. Jako **zdrojovou cestu**zadejte cestu k prostředkům, které chcete uložit do mezipaměti. Pokud chcete v doméně, kterou jste zadali v kroku 5, ukládat do mezipaměti jakýkoliv prostředek, nechte toto nastavení prázdné.
    
7. Do pole **Hlavička počátečního hostitele** zadejte hlavičku hostitele, kterou má Azure CDN odeslat spolu s každou žádostí, nebo ponechte výchozí nastavení.
   
   > [!NOTE]
   > Některé typy původu (například Azure Storage a Web Apps) vyžadují, aby se hlavička hostitele shodovala s doménou původu. Pokud nemáte původ, který vyžaduje hlavičku hostitele odlišnou od své domény, je vhodné ponechat výchozí hodnotu.
   > 
    
8. V poli **protokol** a **zdrojový port**zadejte protokoly a porty, které se mají použít pro přístup k prostředkům na zdrojovém serveru. Je nutné vybrat alespoň jeden protokol (HTTP nebo HTTPS). Pro přístup k obsahu HTTPS použijte doménu poskytovanou CDN ( _\<endpointname>_ . azureedge.NET). 
   
   > [!NOTE]
   > Hodnota **počáteční port** určuje pouze port, který koncový bod používá k načtení informací ze zdrojového serveru. Koncový bod jako takový je dostupný jenom koncovým klientům na výchozích portech HTTP a HTTPS (80 a 443), a to bez ohledu na nastavení **Počáteční port**.  
   > 
   > Koncové body v profilech **Azure CDN od Akamai** pro počáteční porty neumožňují použití plného rozsahu portů. Seznam nepovolených portů původu najdete v tématu [Povolené porty původu Azure CDN společnosti Akamai](/previous-versions/azure/mt757337(v=azure.100)).  
   > 
   > Podpora HTTPS pro Azure CDN vlastní domény není podporovaná v **Azure CDN z produktů Akamai** . Další informace najdete v tématu [Konfigurace HTTPS pro vlastní doménu Azure CDN](cdn-custom-ssl.md).
    
9. Pro **optimalizované pro**vyberte typ optimalizace, který nejlépe odpovídá scénáři a typ obsahu, který má koncový bod doručovat. Další informace najdete v tématu [optimalizace Azure CDN pro typ doručování obsahu](cdn-optimization-overview.md).

    V závislosti na typu profilu se podporují následující nastavení typu optimalizace:
    - **Azure CDN Standard z profilů Microsoftu** :
       - [**Obecné doručování webu**](cdn-optimization-overview.md#general-web-delivery)

    - **Azure CDN Standard od Verizon** a **Azure CDN Premium od profilů Verizon** :
       - [**Obecné doručování webu**](cdn-optimization-overview.md#general-web-delivery)
       - [**Akcelerace dynamických webů**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Azure CDN Standard z profilů Akamai** :
       - [**Obecné doručování webu**](cdn-optimization-overview.md#general-web-delivery)
       - [**Obecné streamování médií**](cdn-optimization-overview.md#general-media-streaming)
       - [**Streamování médií videa na vyžádání**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Stažení velkých souborů**](cdn-optimization-overview.md#large-file-download)
       - [**Akcelerace dynamických webů**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Pokud chcete vytvořit nový koncový bod, vyberte **Přidat**.
   
    Koncový bod se po vytvoření zobrazí v seznamu koncových bodů daného profilu.
    
    ![Koncový bod CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Vzhledem k tomu, že rozšíření registrace nějakou dobu trvá, koncový bod není okamžitě dostupný pro použití: 
    - U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
    - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
    - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 90 minut. 
   
    Pokud se pokusíte použít název domény CDN dřív, než se konfigurace koncového bodu rozšíří na servery POP (Point-of-prezence), může se zobrazit stav odpovědi HTTP 404. Pokud jste koncový bod vytvořili několik hodin a stále se zobrazuje stav odpovědi 404, přečtěte si téma [řešení potíží s Azure CDN koncovými body, které vracejí kód stavu 404](cdn-troubleshoot-endpoint.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete koncový bod odstranit, když už ho nepotřebujete, vyberte ho a pak vyberte **Odstranit**. 

## <a name="next-steps"></a>Další kroky
Další informace o vlastních doménách najdete v kurzu pro přidání vlastní domény do koncového bodu CDN.

> [!div class="nextstepaction"]
> [Přidání vlastní domény](cdn-map-content-to-custom-domain.md)


