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
ms.date: 07/01/2019
ms.author: apimpm
ms.openlocfilehash: 59b44dcc9ec3a1f7c274f426a19aa8ed2258db3e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509307"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurace názvu vlastní domény

Když vytvoříte instanci služby Azure API Management, Azure přiřadí, se subdoménou azure-api.net (například `apim-service-name.azure-api.net`). Však můžete zveřejnit koncové body rozhraní API Management pomocí vlastní název vlastní domény, například **contoso.com**. V tomto kurzu se dozvíte, jak mapovat existující vlastní název DNS ke koncovým bodům vystavené instance služby API Management.

> [!WARNING]
> Zákazníci, kteří chtěli používat Připnutí certifikátu pro zlepšení zabezpečení svých aplikací musí použít vlastní název domény > a certifikát, který řídí, ne výchozí certifikát. Zákazníci, kteří připnout výchozího certifikátu místo toho bude > tak pevné závislosti na vlastnostech certifikátu, nad kterými nemáte kontrolu, která není doporučený postup.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést postup popsaný v tomto článku, budete potřebovat:

-   Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Instance služby API Management. Další informace najdete v tématu [vytvoření instance Azure API Management](get-started-create-service-instance.md).
-   Název vlastní domény, který vlastníte. Vlastní název domény, který chcete použít, musíte si opatřili samostatně a hostovaný na serveru DNS. Toto téma neposkytuje pokyny o tom, jak hostovat vlastní název domény.
-   Musíte mít platný certifikát s veřejným i privátním klíčem (. PFX). Subjektu nebo alternativní název subjektu (SAN) musí odpovídat názvu domény (díky tomu instanci služby API Management umožní bezpečně vystavit adresy URL přes protokol SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Nastavení vlastního názvu domény pomocí webu Azure portal

1. Přejděte do instance služby API Management v [webu Azure portal](https://portal.azure.com/).
1. Vyberte **vlastní domény a SSL**.

    Existuje několik koncových bodů, ke kterým přiřadíte vlastního názvu domény. V současné době jsou k dispozici následující koncové body:

    - **Proxy** (výchozí hodnota je: `<apim-service-name>.azure-api.net`),
    - **Portál** (výchozí hodnota je: `<apim-service-name>.portal.azure-api.net`),
    - **Správa** (výchozí hodnota je: `<apim-service-name>.management.azure-api.net`),
    - **Správce řízení služeb** (výchozí hodnota je: `<apim-service-name>.scm.azure-api.net`).

    > [!NOTE]
    > Můžete aktualizovat všechny koncové body nebo některé z nich. Běžně, zákazníci provést aktualizaci **Proxy** (Tato adresa URL slouží k volání rozhraní API prostřednictvím API Management) a **portál** (portál pro vývojáře adresy URL). **Správa** a **SCM** koncové body se používá interně pouze vlastníci instance API Management a proto jsou méně často přiřazeny vlastního názvu domény. Ve většině případů lze nastavit pouze jeden vlastní název domény pro daný koncový bod. Ale **Premium** úroveň podporuje několik názvy hostitelů pro nastavení **Proxy** koncového bodu.

1. Vyberte koncový bod, který chcete aktualizovat.
1. V okně na pravé straně, klikněte na tlačítko **vlastní**.

    - V **vlastní název domény**, zadejte název, který chcete použít. Například, `api.contoso.com`. Názvy domény se zástupnými znaky (například \*. uživatel@doména.com) jsou také podporovány.
    - V **certifikát**, vyberte certifikát ze služby Key Vault. Můžete také nahrát platný. PFX souboru a zadejte jeho **heslo**v případě, že certifikát je chráněný heslem.

    > [!TIP]
    > Doporučujeme používat Azure Key Vault pro správu certifikátů a nastavení na autorotate.
    > Pokud používáte Azure Key Vault ke správě certifikát SSL pro vlastní doménu, ujistěte se, že certifikát je vložen do služby Key Vault [jako _certifikát_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), nikoli _tajný klíč_.
    >
    > Načíst certifikát protokolu SSL, API Management oprávnění seznamu get tajných kódů v Azure Key Vault, který obsahuje certifikát. Při použití webu Azure portal se automaticky dokončit všechny kroky nezbytné konfigurace. Při použití nástroje příkazového řádku nebo rozhraní API pro správu, musí být tato oprávnění udělena ručně. To se provádí ve dvou krocích. Nejprve pomocí spravované identity stránky na vaší instance služby API Management Ujistěte se, že je povolena Identity spravované a poznamenejte si id objektu zabezpečení, které jsou uvedené na této stránce. Za druhé poskytnout seznam oprávnění a zajistěte si oprávnění tajných kódů pro toto id objektu zabezpečení v Azure Key Vault, který obsahuje certifikát.
    >
    > Pokud tento certifikát je nastavena na autorotate, API Management vyzvedne, až bude na nejnovější verzi automaticky bez odstávky do služby (Pokud je úroveň vaší správy rozhraní API se neuzavírá smlouva SLA – tj. ve všech úrovních, s výjimkou na úrovni Developer).

1. Kliknutím na tlačítko použít.

    > [!NOTE]
    > Proces přiřazení certifikátu může trvat 15 minut nebo déle v závislosti na velikosti nasazení. Vývojář skladová položka má výpadek, Basic a vyšších Skladových položek nemají výpadek.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Další postup

[Upgrade a škálování služby](upgrade-and-scale.md)
