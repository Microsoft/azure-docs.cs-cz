---
title: Konfigurace názvu vlastní domény pro instanci Azure API Management
titleSuffix: Azure API Management
description: Toto téma popisuje, jak nakonfigurovat vlastní název domény pro instanci Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 4587909ad6fca6cdf21d54d11d89f797bbb29833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335836"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurace názvu vlastní domény

Když vytvoříte instanci služby Azure API Management, `azure-api.net` Azure jí `apim-service-name.azure-api.net`přiřadí subdoménu (například). Koncové body správy rozhraní API však můžete vystavit pomocí vlastního názvu domény, například **contoso.com**. Tento kurz ukazuje, jak mapovat existující vlastní název DNS na koncové body vystavené instancí správy rozhraní API.

> [!IMPORTANT]
> Správa rozhraní API přijímá pouze požadavky s hodnotami [záhlaví hostitele,](https://tools.ietf.org/html/rfc2616#section-14.23) které odpovídají výchozímu názvu domény nebo libovolnému z nakonfigurovaných vlastních názvů domén.

> [!WARNING]
> Zákazníci, kteří chtějí používat připnutí certifikátu ke zlepšení zabezpečení svých aplikací, musí používat vlastní název domény a certifikát, který spravují, nikoli výchozí certifikát. Zákazníci, kteří místo toho připnou výchozí certifikát, budou mít tvrdou závislost na vlastnostech certifikátu, který neřídí, což není doporučený postup.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky popsané v tomto článku, musíte mít:

-   Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Instance správy rozhraní API. Další informace najdete [v tématu Vytvoření instance Azure API Management](get-started-create-service-instance.md).
-   Vlastní název domény, který vlastníte vy nebo vaše organizace. Toto téma neobsahuje pokyny, jak získat vlastní název domény.
-   Záznam CNAME hostovaný na serveru DNS, který mapuje název vlastní domény na výchozí název domény instance api management. Toto téma neobsahuje pokyny k hostování záznamu CNAME.
-   Musíte mít platný certifikát s veřejným a soukromým klíčem (. PFX). Předmět nebo předmět alternativní název (SAN) musí odpovídat názvu domény (to umožňuje instanci api management bezpečně vystavit adresy URL přes TLS).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Nastavení vlastního názvu domény pomocí portálu Azure Portal

1. Přejděte na instanci správy rozhraní API na [webu Azure Portal](https://portal.azure.com/).
1. Vyberte **Vlastní domény**.

    Existuje několik koncových bodů, ke kterým můžete přiřadit vlastní název domény. V současné době jsou k dispozici následující koncové body:

    - **Brána** (výchozí `<apim-service-name>.azure-api.net`hodnota je: ),
    - **Portál** (výchozí `<apim-service-name>.portal.azure-api.net`hodnota je: ),
    - **Management** (výchozí `<apim-service-name>.management.azure-api.net`hodnota je: ),
    - **SCM** (výchozí `<apim-service-name>.scm.azure-api.net`hodnota je: ),
    - **NewPortal** (výchozí `<apim-service-name>.developer.azure-api.net`hodnota je: ).

    > [!NOTE]
    > Pouze koncový bod **brány** je k dispozici pro konfiguraci ve vrstvě Spotřebu.
    > Můžete aktualizovat všechny koncové body nebo některé z nich. Zákazníci obvykle aktualizují **bránu** (tato adresa URL se používá k volání rozhraní API vystaveného prostřednictvím správy rozhraní API) a **portálu** (adresa URL portálu pro vývojáře).
    > **Koncové** body správy a **SCM** jsou používány interně pouze vlastníky instance api management, a proto jsou méně často přiřazeny vlastní název domény.
    > Úroveň **Premium** podporuje nastavení více názvů hostitelů pro koncový bod **brány.**

1. Vyberte koncový bod, který chcete aktualizovat.
1. V okně vpravo klikněte na **Vlastní**.

    - V poli **Vlastní název domény**zadejte název, který chcete použít. Například, `api.contoso.com`.
    - V **certifikátu**vyberte certifikát z trezoru klíčů. Můžete také nahrát platný . PFX a poskytnout jeho **heslo**, pokud je certifikát chráněn heslem.

    > [!NOTE]
    > Názvy domén se zástupnými `*.contoso.com` symboly, například jsou podporovány ve všech úrovních kromě úrovně Consumption.

    > [!TIP]
    > Doporučujeme používat Azure Key Vault pro správu certifikátů a jejich nastavení na automatické otáčení.
    > Pokud ke správě certifikátu TLS/SSL vlastní domény používáte trezor klíčů Azure, ujistěte se, že je certifikát vložen do trezoru klíčů [jako _certifikát_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), nikoli _jako tajný klíč_.
    >
    > Chcete-li načíst certifikát TLS/SSL, musí mít správa rozhraní API seznam a získat oprávnění k tajným klíčům v trezoru klíčů Azure obsahujícím certifikát. Při použití portálu Azure se automaticky dokončí všechny potřebné kroky konfigurace. Při použití nástrojů příkazového řádku nebo rozhraní API pro správu musí být tato oprávnění udělena ručně. To se provádí ve dvou krocích. Nejprve použijte stránku Spravované identity v instanci správy rozhraní API, abyste se ujistili, že je povolená spravovaná identita, a poznamenejte si hlavní id zobrazené na této stránce. Za druhé, udělit seznam oprávnění a získat oprávnění tajných kódů k tomuto id hlavního moc na Azure Key Vault obsahující certifikát.
    >
    > Pokud je certifikát nastaven na automatické otáčení, správa rozhraní API automaticky převezme nejnovější verzi bez prostojů služby (pokud má vaše úroveň Správy rozhraní API sla – tj. ve všech úrovních kromě úrovně Vývojář).

1. Klikněte na Použít.

    > [!NOTE]
    > Proces přiřazení certifikátu může trvat 15 minut nebo více v závislosti na velikosti nasazení. Vývojářská skladová položka má prostoje, základní a vyšší skladové položky nemají prostoje.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Konfigurace DNS

Při konfiguraci služby DNS pro vlastní název domény máte dvě možnosti:

-   Nakonfigurujte záznam CNAME, který odkazuje na koncový bod nakonfigurovaného názvu vlastní domény.
-   Nakonfigurujte záznam A, který odkazuje na ip adresu brány api managementu.

> [!NOTE]
> Přestože ip adresa instance řízení rozhraní API je statická, může se v několika scénářích změnit. Z tohoto důvodu se doporučuje používat CNAME při konfiguraci vlastní domény. Vezměte to v úvahu při výběru metody konfigurace DNS. Další informace naleznete v [článku dokumentace k IP adresám](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) a [nejčastějším dotazům](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)na správu rozhraní API .

## <a name="next-steps"></a>Další kroky

[Upgrade a škálování služeb](upgrade-and-scale.md)
