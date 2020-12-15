---
title: Předplatná v Azure API Management | Microsoft Docs
description: Přečtěte si o konceptu předplatných v Azure API Management. Příjemci získají přístup k rozhraním API pomocí předplatných v Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: cdc7668b06308bd5532f4885d0e1365fc34ae989
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511242"
---
# <a name="subscriptions-in-azure-api-management"></a>Předplatná ve službě Azure API Management

Předplatná jsou důležitou koncepcí v Azure API Management. Jedná se o nejběžnější způsob, jak mohou příjemci rozhraní API získat přístup k rozhraním API publikovaným pomocí instance API Management. Tento článek poskytuje přehled konceptu.

## <a name="what-are-subscriptions"></a>Co jsou předplatné?

Když publikujete rozhraní API prostřednictvím API Management, je snadné a běžné zabezpečení přístupu k těmto rozhraním API pomocí klíčů předplatného. Vývojáři, kteří potřebují využívat publikovaná rozhraní API, musí při volání do těchto rozhraní API zahrnovat platný klíč předplatného v požadavcích HTTP. V opačném případě jsou volání okamžitě odmítnuta bránou API Management. Neodesílají se do back-endové služby.

Pokud chcete získat klíč předplatného pro přístup k rozhraním API, vyžaduje se předplatné. Předplatné je v podstatě pojmenovaný kontejner pro pár klíčů předplatného. Vývojáři, kteří potřebují využívat publikovaná rozhraní API, můžou získat odběry. A nepotřebují schválení od vydavatelů rozhraní API. Vydavatelé rozhraní API můžou také vytvářet odběry přímo pro uživatele rozhraní API.

> [!TIP]
> API Management také podporuje další mechanismy pro zabezpečení přístupu k rozhraním API, včetně následujících příkladů:
> - [OAuth 2.0](api-management-howto-protect-backend-with-aad.md)
> - [Klientské certifikáty](api-management-howto-mutual-certificates-for-clients.md)
> - [Omezení IP adres volajícího](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Rozsah předplatných

Odběry je možné přidružit k různým oborům: produkt, všechna rozhraní API nebo jednotlivá rozhraní API.

### <a name="subscriptions-for-a-product"></a>Předplatná produktu

Předplatná v API Management byla tradičně přidružena k jednomu oboru [produktu API](api-management-terminology.md) . Vývojáři nalezli seznam produktů na portálu pro vývojáře. Pak by v rámci předplatného odeslali požadavky na produkty, které chtěli používat. Po schválení žádosti o předplatné buď automaticky, nebo vydavatelům rozhraní API může vývojář použít klíče v tomto produktu pro přístup ke všem rozhraním API v produktu. Portál pro vývojáře v současnosti zobrazuje jenom odběry oborů produktů v části Profil uživatele. 

![Předplatná produktů](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> V některých případech můžou vydavatelé rozhraní API chtít publikovat produkt API na veřejném, aniž by museli mít požadavek na odběry. V Azure Portal můžou zrušit výběr možnosti **vyžadovat předplatné** na stránce **Nastavení** produktu. V důsledku toho je přístup k všem rozhraním API v rámci produktu možné bez klíče rozhraní API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Předplatná pro všechna rozhraní API nebo jednotlivá rozhraní API

Až zavedeme úroveň [spotřeby](https://aka.ms/apimconsumptionblog) API Management, provedli jsme několik změn, které zjednodušují správu klíčů:
- Nejdřív jsme přidali dva další obory předplatného: všechna rozhraní API a jedno rozhraní API. Rozsah předplatných už není omezený na produkt rozhraní API. Nyní je možné vytvořit klíče, které udělí přístup k rozhraní API nebo všechna rozhraní API v rámci instance API Management, aniž byste museli vytvořit produkt a přidat k němu rozhraní API jako první. Každá instance API Management nyní přináší neměnné předplatné All-API. Toto předplatné usnadňuje a zjednodušuje testování a ladění rozhraní API v rámci testovací konzoly.

- V druhém API Management nyní umožňuje **samostatné** odběry. Odběry již není nutné přidružit k účtu vývojáře. Tato funkce je užitečná ve scénářích, jako je například když několik vývojářů nebo týmů sdílí předplatné.

- A konečně, vydavatelé rozhraní API teď můžou [vytvářet předplatná](api-management-howto-create-subscriptions.md) přímo v Azure Portal:

    ![Flexibilní odběry](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Další kroky
Získat další informace o API Management:

+ Přečtěte si další [Koncepty](api-management-terminology.md) v API Management.
+ Další informace o API Management najdete v našich [kurzech](import-and-publish.md) .
+ Nejčastější dotazy najdete na [stránce s nejčastějšími dotazy](api-management-faq.md) .
