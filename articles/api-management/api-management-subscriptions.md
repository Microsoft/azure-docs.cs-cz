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
ms.openlocfilehash: 1f7c2bde631e42ac94ce4c0394d13e369f6ea23f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54049754"
---
# <a name="subscriptions-in-azure-api-management"></a>Předplatná ve službě Azure API Management

Předplatné je důležitý koncept ve službě Azure API Management. Jsou to nejběžnější způsob pro zákazníky se získat přístup k rozhraním API publikované prostřednictvím instance služby API Management. Tento článek obsahuje přehled koncept.

## <a name="what-are-subscriptions"></a>Co jsou předplatná?

Při publikování rozhraní API prostřednictvím služby API Management je snadné a společné pro zabezpečený přístup k těmto rozhraním API pomocí klíče předplatného. Vývojáři, kteří potřebují využívat publikovaných rozhraní API musí obsahovat platné předplatné klíč v požadavcích HTTP při provádění volání těchto rozhraní API. V opačném případě volání odmítne okamžitě brány API Management. Nepředávají se k back endové služby.

Pokud chcete získat klíč předplatného pro přístup k rozhraní API, se vyžaduje předplatné. Předplatné je v podstatě pojmenované kontejner pro dvojice klíčů předplatného. Vývojáři, kteří potřebují využívat publikovaných rozhraní API můžete získat předplatná. A nepotřebují schválení od vydavatelé rozhraní API. Vydavatelé rozhraní API můžete také vytvořit odběry přímo pro zákazníky.

> [!TIP]
> API Management podporuje také další mechanismy pro zabezpečení přístupu k rozhraní API, včetně následujících příkladech:
> - [OAuth 2.0](api-management-howto-protect-backend-with-aad.md)
> - [Klientské certifikáty](api-management-howto-mutual-certificates-for-clients.md)
> - [Přidávání na seznam povolených IP](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Obor předplatných

Předplatná můžou být spojené s různými obory: produkt, všechna rozhraní API nebo jednotlivé rozhraní API.

### <a name="subscriptions-for-a-product"></a>Předplatná produktu

Tradičně, předplatná ve službě API Management souvisely vždy pomocí jediného [produktu API](api-management-terminology.md) oboru. Vývojáři najít seznam produktů na portálu pro vývojáře. Pak by odesílání žádostí o odběr pro produkty, že ale chtěli použít. Po schválení žádosti o odběr, automaticky nebo které vydavatelé rozhraní API, Vývojář můžete klíče v něm přístup ke všem rozhraním API v rámci produktu.

![Předplatná produktu](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> V některých případech vydavatelé rozhraní API může být vhodné publikování produktu rozhraní API veřejně bez nutnosti předplatných. Můžete zrušit **vyžadují předplatné** možnost **nastavení** stránky produktu na webu Azure Portal. Všechna rozhraní API v rámci produktu v důsledku toho lze přistupovat bez klíče rozhraní API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Předplatná pro všechna rozhraní API nebo jednotlivé rozhraní API

> [!NOTE]
> Tato funkce je aktuálně dostupná jenom na úrovni řízení spotřeby API.

Když jsme představili [spotřeby](https://aka.ms/apimconsumptionblog) úroveň služby API Management jsme provedli několik změn zefektivnit správu klíčů:
- Nejprve jsme přidali dvě další obory předplatného: všechna rozhraní API a jediné rozhraní API. Obor předplatného už nejsou omezené na produktu rozhraní API. Nyní je možné vytvořit klíče, které udělit přístup k rozhraní API nebo všechna rozhraní API v rámci instance služby API Management, aniž by bylo nutné vytvořit produkt a do ní nejprve přidat rozhraní API. Kromě toho každá instance služby API Management nyní zahrnuje předplatné neměnné a všechna rozhraní API. Toto předplatné usnadňuje a jednodušší kvůli testování a ladění rozhraní API v testovací konzole.

- Za druhé, API Management nyní umožňuje **samostatné** předplatných. Předplatná jsou už musí být přidružené k účtu pro vývojáře. Tato funkce je užitečná ve scénářích, například pokud několik vývojáře nebo týmy sdílet předplatné.

- Nakonec se nyní mohou vydavatelé rozhraní API [vytvářet předplatná](api-management-howto-create-subscriptions.md) přímo na webu Azure portal:

    ![Flexibilní předplatná](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Další postup
Získejte další informace o službě API Management:

+ Přečtěte si další [koncepty](api-management-terminology.md) ve službě API Management.
+ Postupujte podle našich [kurzy](import-and-publish.md) Další informace o službě API Management.
+ Zkontrolujte naše [stránku s nejčastějšími dotazy](api-management-faq.md) pro běžné dotazy.