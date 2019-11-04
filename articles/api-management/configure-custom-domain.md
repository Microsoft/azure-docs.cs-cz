---
title: Konfigurace vlastního názvu domény pro vaši instanci Azure API Management | Microsoft Docs
description: Toto téma popisuje, jak nakonfigurovat vlastní název domény pro instanci Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: ba2288fa4add6df6b7861b5f8a99bcd3ee8b4c12
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472194"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurace názvu vlastní domény

Když vytvoříte instanci služby Azure API Management, Azure mu přiřadí subdoménu azure-api.net (například `apim-service-name.azure-api.net`). Můžete ale zpřístupnit API Management koncových bodů pomocí vlastního názvu domény, například **contoso.com**. V tomto kurzu se dozvíte, jak namapovat stávající vlastní název DNS na koncové body vystavené instancí API Management.

> [!WARNING]
> Zákazníci, kteří chtějí použít připnutí certifikátů ke zlepšení zabezpečení svých aplikací, musí používat vlastní název domény > a certifikát, který spravují, nikoli výchozí certifikát. Zákazníci, kteří připnout výchozí certifikát, místo toho budou > s ohledem na vlastnosti certifikátu, který neovládají, což není doporučený postup.

## <a name="prerequisites"></a>Předpoklady

K provedení kroků popsaných v tomto článku musíte mít:

-   Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Instance API Management. Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
-   Vlastní název domény, který vlastníte vy nebo vaše organizace. Toto téma neposkytuje pokyny k získání vlastního názvu domény.
-   Záznam CNAME hostovaný na serveru DNS, který mapuje název vlastní domény na výchozí název domény vaší instance API Management. Toto téma neposkytuje pokyny k hostování záznamu CNAME.
-   Musíte mít platný certifikát s veřejným i privátním klíčem (. PFX). Alternativní název předmětu nebo subjektu (SAN) musí odpovídat názvu domény (to umožňuje API Management instance bezpečně vystavit adresy URL přes SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Použijte Azure Portal k nastavení vlastního názvu domény.

1. V [Azure Portal](https://portal.azure.com/)přejděte ke své instanci API Management.
1. Vyberte **vlastní domény**.

    K dispozici je několik koncových bodů, ke kterým můžete přiřadit vlastní název domény. V současné době jsou k dispozici následující koncové body:

    - **Brána** (výchozí je: `<apim-service-name>.azure-api.net`),
    - **Portál** (výchozí je: `<apim-service-name>.portal.azure-api.net`),
    - **Správa** (výchozí je: `<apim-service-name>.management.azure-api.net`),
    - **SCM** (výchozí je: `<apim-service-name>.scm.azure-api.net`).

    > [!NOTE]
    > Pro konfiguraci v úrovni spotřeby je dostupný jenom koncový bod **brány** .
    > Můžete aktualizovat všechny koncové body nebo některé z nich. Zákazníci aktualizují **bránu** (Tato adresa URL se běžně používá k volání rozhraní API vystaveného prostřednictvím API Management) a **portálu** (adresa URL portálu pro vývojáře).
    > Koncové body **správy** a **SCM** se používají interně pouze vlastníky instance API Management, takže se jim pro vlastní název domény často přiřazují méně často.
    > Úroveň **Premium** podporuje nastavení více názvů hostitelů pro koncový bod **brány** .

1. Vyberte koncový bod, který chcete aktualizovat.
1. V okně na pravé straně klikněte na **vlastní**.

    - Do pole **vlastní název domény**zadejte název, který chcete použít. Například, `api.contoso.com`.
    - V části **certifikát**vyberte certifikát z Key Vault. Můžete také nahrát platnou hodnotu. Soubor PFX a zadejte **heslo**, pokud je certifikát chráněn heslem.

    > [!NOTE]
    > Názvy domén se zástupnými znaky, například `*.contoso.com`, jsou podporovány ve všech vrstvách s výjimkou úrovně spotřeby.

    > [!TIP]
    > Doporučujeme použít Azure Key Vault pro správu certifikátů a jejich nastavení na automatické střídání.
    > Pokud používáte Azure Key Vault ke správě vlastního certifikátu SSL domény, ujistěte se, že je certifikát vložen do Key Vault [jako _certifikát_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), nikoli jako _tajný kód_.
    >
    > Aby bylo možné načíst certifikát protokolu SSL, API Management musí mít v Azure Key Vault obsahujícím certifikát oprávnění Get tajných klíčů. Při použití Azure Portal všechny nezbytné kroky konfigurace se automaticky dokončí. Při použití nástrojů příkazového řádku nebo rozhraní API pro správu musí být tato oprávnění udělena ručně. To se provádí ve dvou krocích. Nejprve pomocí stránky spravované identity na vaší instanci API Management zajistěte, aby byla povolena spravovaná identita, a poznamenejte si ID objektu zabezpečení zobrazené na této stránce. Druhý, udělte seznam oprávnění a získejte přístup k tomuto ID objektu zabezpečení pro Azure Key Vault obsahující certifikát.
    >
    > Pokud je certifikát nastavený na automatické střídání, API Management se automaticky vybere nejnovější verze bez jakéhokoli výpadku služby (Pokud vaše API Management vrstva má smlouvu SLA – i. e. ve všech vrstvách kromě úrovně pro vývojáře).

1. Klikněte na použít.

    > [!NOTE]
    > Proces přiřazení certifikátu může v závislosti na velikosti nasazení trvat 15 minut nebo déle. SKU pro vývojáře má výpadky, základní a vyšší skladové jednotky nevýpadek.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Konfigurace DNS

Při konfiguraci DNS pro vlastní název domény máte dvě možnosti:

-   Nakonfigurujte záznam CNAME, který odkazuje na koncový bod vašeho nakonfigurovaného vlastního názvu domény.
-   Nakonfigurujte záznam A, který odkazuje na IP adresu brány API Management.

> [!NOTE]
> I když je IP adresa instance pro správu rozhraní API statická, může se v několika scénářích změnit. Z tohoto důvodu doporučujeme při konfiguraci vlastní domény použít CNAME. Při volbě metody konfigurace DNS Vezměte v úvahu. Další informace najdete v části [Nejčastější dotazy k API Management](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services).

## <a name="next-steps"></a>Další kroky

[Upgrade a škálování služby](upgrade-and-scale.md)
