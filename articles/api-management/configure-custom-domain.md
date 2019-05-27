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
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: a8bfa7c5baa316b4019480bfc146b6cc61eff979
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66141304"
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

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Nastavení vlastního názvu domény pomocí webu Azure portal

1. Přejděte k vaší instanci APIM v [webu Azure portal](https://portal.azure.com/).
1. Vyberte **vlastní domény a SSL**.
    
    Existuje několik koncových bodů, ke kterým přiřadíte vlastního názvu domény. V současné době jsou k dispozici následující koncové body: 
   + **Proxy** (výchozí hodnota je: `<apim-service-name>.azure-api.net`), 
   + **Portál** (výchozí hodnota je: `<apim-service-name>.portal.azure-api.net`),     
   + **Správa** (výchozí hodnota je: `<apim-service-name>.management.azure-api.net`), 
   + **Správce řízení služeb** (výchozí hodnota je: `<apim-service-name>.scm.azure-api.net`).

     >[!NOTE]
     > Můžete aktualizovat všechny koncové body nebo některé z nich. Běžně, zákazníci provést aktualizaci **Proxy** (Tato adresa URL slouží k volání rozhraní API prostřednictvím API Management) a **portál** (portál pro vývojáře adresy URL). **Správa** a **SCM** koncové body se používají interně zákazníci služby APIM a proto jsou méně často přiřazeny vlastního názvu domény.

1. Vyberte koncový bod, který chcete aktualizovat. 
1. V okně na pravé straně, klikněte na tlačítko **vlastní**.

   + V **vlastní název domény**, zadejte název, který chcete použít. Například, `api.contoso.com`. Názvy domény se zástupnými znaky (například *. uživatel@doména.com) jsou také podporovány.
   + V **certifikát**, vyberte certifikát ze služby Key Vault. Můžete také nahrát platný. PFX souboru a zadejte jeho **heslo**v případě, že certifikát je chráněný heslem.

     > [!TIP]
     > Pokud používáte Azure Key Vault ke správě certifikát SSL pro vlastní doménu, ujistěte se, že certifikát je vložen do služby Key Vault [jako *certifikát*](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), nikoli *tajný klíč*. Pokud tento certifikát je nastavena na autorotate, API Management na nejnovější verzi automaticky převezmou.

1. Kliknutím na tlačítko použít.

    >[!NOTE]
    >Proces přiřazení certifikátu může trvat 15 minut nebo déle v závislosti na velikosti nasazení. Vývojář skladová položka má výpadek, Basic a vyšších Skladových položek nemají výpadek.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Další postup

[Upgrade a škálování služby](upgrade-and-scale.md)
