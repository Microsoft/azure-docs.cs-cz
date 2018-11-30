---
title: Předplatná ve službě Azure API Management | Dokumentace Microsoftu
description: Další informace o konceptu předplatných ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 45a65c7a94f3e6917b2882f27c9ad7d764ef97d7
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621797"
---
# <a name="subscriptions-in-azure-api-management"></a>Předplatná ve službě Azure API Management

Předplatné je důležitý koncept v Azure API Management (APIM). Je nejběžnější způsob pro zákazníky se získat přístup k rozhraní API publikované prostřednictvím instanci služby APIM. Tento článek obsahuje přehled koncept.

## <a name="what-is-subscriptions"></a>Co je předplatné

Při publikování rozhraní API pomocí služby APIM, je nejběžnější a nejjednodušší způsob, jak zabezpečit přístup k těmto rozhraním API pomocí klíče předplatného. Jinými slovy vývojáře, kteří potřebují využívat publikovaných rozhraní API musí obsahovat platný klíč předplatného v požadavcích HTTP při volání těchto rozhraních API. Jinak volání okamžitě odmítne bránu služby APIM a se předávají do back-endové služby.

Pokud chcete získat klíč předplatného pro přístup k rozhraní API, se vyžaduje předplatné. Předplatné je v podstatě pojmenované kontejner pro dvojice klíčů předplatného. Předplatná je možné získat vývojáři, kteří potřebují využívat publikovaných rozhraní API, s nebo bez souhlasu vydavatelé rozhraní API. Vydavatelé rozhraní API můžete také vytvořit odběry přímo, jménem zákazníky.

> [!TIP]
> APIM také podporuje další mechanismy pro zabezpečení přístupu k rozhraní API včetně [OAuth 2.0](api-management-howto-protect-backend-with-aad.md), [klientské certifikáty](api-management-howto-mutual-certificates-for-clients.md), a [přidávání na seznam povolených IP](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Obor předplatných

Předplatná můžou být spojené s různými obory: produkt, všechna rozhraní API nebo jednotlivé rozhraní API.

### <a name="subscriptions-for-a-product"></a>Předplatná produktu

Tradičně předplatných služby APIM souvisely vždy pomocí jediného [produktu API](api-management-terminology.md) oboru. Vývojáři by najít seznam produktů na portálu pro vývojáře a odesílání žádostí o odběr pro produkty, které byste chtěli použít. Jakmile se předplatné žádost o schválení (automaticky nebo které vydavatelé rozhraní API), Vývojář můžete klíče v něm přístup ke všem rozhraním API v rámci produktu.

![Předplatná produktu](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> V některých případech vydavatelé rozhraní API chtít publikování produktu rozhraní API veřejně bez nutnosti předplatných. To jde, zrušte zaškrtnutí políčka **vyžadují předplatné** možnost **nastavení** stránky produktu na webu Azure Portal. Všechna rozhraní API v rámci produktu v důsledku toho lze přistupovat bez klíče rozhraní API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Předplatná pro všechna rozhraní API nebo jednotlivé rozhraní API

> [!NOTE]
> Tato funkce je aktuálně dostupná jenom na úrovni řízení spotřeby API.

Když jsme představili [spotřeby](https://aka.ms/apimconsumptionblog) vrstvy ze služby APIM, jsme provedli několik změn zefektivnit správu klíčů. Nejprve jsme přidali dvě další obory předplatné – všechna rozhraní API a jediné rozhraní API. Obor předplatného už nejsou omezené na produktu rozhraní API. Nyní je možné vytvořit klíče udělení přístupu k rozhraní API (nebo všechna rozhraní API v instanci služby APIM), bez nutnosti nejprve přidat rozhraní API k němu a vytvořte produkt. Kromě toho každá instance APIM nyní obsahuje předplatné neměnné a všechna rozhraní API, které usnadňuje a jednodušší kvůli testování a ladění rozhraní API v testovací konzole.

Za druhé APIM teď umožňuje předplatná "standalone". Předplatná jsou už musí být přidružené k účtu pro vývojáře. To je užitečné v situacích, například když je předplatné sdílet více vývojáři nebo týmů.

Nakonec se nyní mohou vydavatelé rozhraní API [vytvářet předplatná](api-management-howto-create-subscriptions.md) přímo na webu Azure Portal:

![Flexibilní předplatná](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Další postup
Další informace o službě API Management:

+ Přečtěte si další [koncepty](api-management-terminology.md) ve službě API Management
+ Postupujte podle našich [kurzy](import-and-publish.md) Další informace o službě API Management
+ Zkontrolujte naše [stránku s nejčastějšími dotazy](api-management-faq.md) pro běžné dotazy