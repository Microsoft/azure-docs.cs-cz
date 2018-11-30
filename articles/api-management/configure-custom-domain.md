---
title: Konfigurace vlastního názvu domény pro vaše instance Azure API Management | Dokumentace Microsoftu
description: Toto téma popisuje postup konfigurace vlastního názvu domény pro vaše instance Azure API Management.
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
ms.openlocfilehash: f613995dbdd787d0a031cb2c24d67c682b2d7cec
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446364"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurace názvu vlastní domény 

Při vytvoření instance API Management (APIM), Azure ho přiřadí k subdoménou azure-api.net (například `apim-service-name.azure-api.net`). Však můžete zveřejnit svůj vlastní název domény, například pomocí koncových bodů služby APIM **contoso.com**. V tomto kurzu se dozvíte, jak mapovat existující vlastní název DNS ke koncovým bodům vystavené instance Azure API Management.

> [!WARNING]
> Zákazníci, kteří chtěli používat Připnutí certifikátu pro zlepšení zabezpečení svých aplikací musí použít vlastní název domény > a certifikát, který řídí, ne výchozí certifikát. Zákazníci, kteří připnout výchozího certifikátu místo toho bude > tak pevné závislosti na vlastnostech certifikátu, nad kterými nemáte kontrolu, která není doporučený postup.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést postup popsaný v tomto článku, budete potřebovat:

+ Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Instanci služby APIM. Další informace najdete v tématu [vytvoření instance Azure API Management](get-started-create-service-instance.md).
+ Název vlastní domény, který vlastníte. Vlastní název domény, který chcete použít, musíte si opatřili samostatně a hostovaný na serveru DNS. Toto téma neposkytuje pokyny o tom, jak hostovat vlastní název domény.
+ Musíte mít platný certifikát s veřejným i privátním klíčem (. PFX). Subjektu nebo alternativní název subjektu (SAN) musí odpovídat názvu domény (díky tomu APIM umožní bezpečně vystavit adresy URL přes protokol SSL).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Nastavení vlastního názvu domény pomocí webu Azure portal

1. Přejděte k vaší instanci APIM v [webu Azure portal](https://portal.azure.com/).
2. Vyberte **vlastní domény a SSL**.
    
    Existuje několik koncových bodů, ke kterým přiřadíte vlastního názvu domény. V současné době jsou k dispozici následující koncové body: 
    + **Proxy** (výchozí hodnota je: `<apim-service-name>.azure-api.net`), 
    + **Portál** (výchozí hodnota je: `<apim-service-name>.portal.azure-api.net`),     
    + **Správa** (výchozí hodnota je: `<apim-service-name>.management.azure-api.net`), 
    + **Správce řízení služeb** (výchozí hodnota je: `<apim-service-name>.scm.azure-api.net`).

    >[!NOTE]
    > Můžete aktualizovat všechny koncové body nebo některé z nich. Běžně, zákazníci provést aktualizaci **Proxy** (Tato adresa URL slouží k volání rozhraní API prostřednictvím API Management) a **portál** (portál pro vývojáře adresy URL). **Správa** a **SCM** koncové body se používají interně zákazníci služby APIM a proto jsou méně často přiřazeny vlastního názvu domény.
3. Vyberte koncový bod, který chcete aktualizovat. 
4. V okně na pravé straně, klikněte na tlačítko **vlastní**.

    + V **vlastní název domény**, zadejte název, který chcete použít. Například, `api.contoso.com`. <br/>Názvy domény se zástupnými znaky (například *. uživatel@doména.com) jsou také podporovány.
    + V **certifikát**, zadejte platný. Soubor PFX, který chcete nahrát. 
    + Má-li certifikát heslo, zadejte ho **heslo** pole.
1. Kliknutím na tlačítko použít.

    >[!NOTE]
    >Proces přiřazení certifikátu může trvat 15 minut nebo déle v závislosti na velikosti nasazení. Vývojář skladová položka má výpadek, Basic a vyšších Skladových položek nemají výpadek.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Další postup

[Upgrade a škálování služby](upgrade-and-scale.md)
