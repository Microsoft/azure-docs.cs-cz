---
title: Konfigurace vlastního názvu domény pro vaše instance služby Azure API Management | Microsoft Docs
description: Toto téma popisuje postup konfigurace vlastního názvu domény pro vaše instance služby Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: 96e233a26af95d4373323867046ca01fe1304608
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2018
ms.locfileid: "29345366"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurace názvu vlastní domény 

Při vytvoření instance API Management (APIM) se Azure přiřadí subdoména azure api.net (například `apim-service-name.azure-api.net`). Ale můžou zpřístupnit APIM koncové body pomocí svůj vlastní název domény, jako třeba **contoso.com**. V tomto kurzu se dozvíte, jak mapovat existující vlastní název DNS koncové body vystavené instance služby Azure API Management.

> [!WARNING]
> Zákazníci, kteří jej nepřejete použít Připnutí certifikátu pro zlepšení zabezpečení svých aplikací musíte použít vlastní název domény > a certifikát, který budou spravovat, není výchozí certifikát. Zákazníkům, kteří připnout výchozí certifikát místo toho bude > trvá pevný závislost na vlastnosti certifikátu se nad nemáte kontrolu, což není doporučený postup.

## <a name="prerequisites"></a>Požadavky

K provedení kroků popsaných v tomto článku, musíte mít:

+ Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM instance. Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
+ Název vlastní domény, který vlastníte. Vlastní název domény, které chcete použít, musíte si opatřili samostatně a hostovaný na serveru DNS. Toto téma neposkytuje pokyny o tom, jak hostovat vlastní název domény.
+ Musí mít platný certifikát s veřejným a soukromým klíčem (. PFX). Subjektu nebo alternativní název předmětu (SAN) musí odpovídat názvu domény (to umožňuje APIM umožní bezpečně vystavit adresy URL přes protokol SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Chcete-li nastavit vlastní název domény použít portál Azure

1. Přejděte k vaší instanci APIM v [portál Azure](https://portal.azure.com/).
2. Vyberte **vlastní domény a SSL**.
    
    Je počet koncových bodů, ke kterým můžete přiřadit vlastní název domény. V současné době jsou k dispozici následující koncové body: 
    + **Proxy** (výchozí hodnota je: `<apim-service-name>.azure-api.net`), 
    + **Portál** (výchozí hodnota je: `<apim-service-name>.portal.azure-api.net`),     
    + **Správa** (výchozí hodnota je: `<apim-service-name>.management.azure-api.net`), 
    + **SCM** (výchozí hodnota je: `<apim-service-name>.scm.azure-api.net`).

    >[!NOTE]
    > Můžete aktualizovat koncové body nebo některá z nich. Běžně, zákazníky aktualizaci **Proxy** (Tato adresa URL se používá k volání rozhraní API vystavenou přes API Management) a **portál** (portál pro vývojáře adresy URL). **Správa** a **SCM** koncové body se používá interně APIM zákazníci a proto jsou méně často přiřazené vlastní název domény.
3. Vyberte koncového bodu, který chcete aktualizovat. 
4. V okně na pravé straně klikněte na **vlastní**.

    + V **vlastního názvu domény**, zadejte název, který chcete použít. Například, `api.contoso.com`. <br/>Zástupné názvy domény (třeba *. domena.com) jsou také podporovány.
    + V **certifikát**, zadejte platný. Soubor PFX, který chcete nahrát. 
    + Pokud má certifikát heslo, zadejte ho **heslo** pole.
1. Kliknutím na tlačítko použít.

    >[!NOTE]
    >Proces přiřazení certifikátu může trvat 15 minut nebo déle v závislosti na velikosti nasazení. Vývojáře SKU má výpadek, Basic a vyšší skladová položka společnosti nemají výpadku.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Další postup

[Upgrade a škálování služby](upgrade-and-scale.md)
