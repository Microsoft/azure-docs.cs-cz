---
title: Předplatná ve správě rozhraní Azure API | Dokumenty společnosti Microsoft
description: Přečtěte si o konceptu předplatných ve správě rozhraní Azure API.
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
ms.openlocfilehash: 9144af131e1427d0b3226655c871921ac1d91665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073411"
---
# <a name="subscriptions-in-azure-api-management"></a>Předplatná ve službě Azure API Management

Předplatná jsou důležitým konceptem ve správě rozhraní Azure API. Jsou to nejběžnější způsob, jak pro spotřebitele rozhraní API získat přístup k rozhraní API publikovaných prostřednictvím instance správy rozhraní API. Tento článek obsahuje přehled konceptu.

## <a name="what-are-subscriptions"></a>Co jsou předplatná?

Při publikování rozhraní API prostřednictvím správy rozhraní API je snadné a běžné zabezpečit přístup k těmto rozhraním API pomocí klíčů předplatného. Vývojáři, kteří potřebují využívat publikovaná rozhraní API, musí při volání těchto rozhraní API zahrnout platný klíč předplatného v požadavcích HTTP. V opačném případě jsou volání okamžitě odmítnuta bránou pro správu rozhraní API. Nejsou předávány do back-endových služeb.

Chcete-li získat klíč předplatného pro přístup k api, je vyžadováno předplatné. Předplatné je v podstatě pojmenovaný kontejner pro dvojici klíčů předplatného. Vývojáři, kteří potřebují využívat publikovaná rozhraní API, můžou získat předplatná. A nepotřebují souhlas vydavatelů rozhraní API. Vydavatelé rozhraní API můžete také vytvářet odběry přímo pro spotřebitele rozhraní API.

> [!TIP]
> Správa rozhraní API také podporuje další mechanismy pro zabezpečení přístupu k rozhraní API, včetně následujících příkladů:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Klientské certifikáty](api-management-howto-mutual-certificates-for-clients.md)
> - [Seznam povolených adres IP](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Rozsah předplatných

Předplatná mohou být přidruženy k různým oborům: produktu, všech rozhraní API nebo individuálnírozhraní API.

### <a name="subscriptions-for-a-product"></a>Předplatná produktu

Tradičně odběry ve správě rozhraní API byly vždy přidruženy k oboru [jednoho produktu rozhraní API.](api-management-terminology.md) Vývojáři našli seznam produktů na portálu pro vývojáře. Pak by odeslat žádosti o předplatné pro produkty, které chtěli použít. Po schválení žádosti o předplatné, buď automaticky, nebo vydavateli rozhraní API, může vývojář použít klíče v něm pro přístup ke všem rozhraním API v produktu. V současné době portál pro vývojáře zobrazuje pouze odběry oboru produktu v části profil uživatele. 

![Předplatné produktů](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> V určitých scénářích mohou vydavatelé rozhraní API chtít publikovat produkt rozhraní API pro veřejnost bez požadavku na odběry. Můžete zrušit výběr **možnost i na** stránce **Nastavení** produktu na webu Azure Portal. V důsledku toho všechna rozhraní API v rámci produktu lze přistupovat bez klíče rozhraní API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Předplatná pro všechna rozhraní API nebo individuální rozhraní API

Když jsme zavedli úroveň [spotřeby](https://aka.ms/apimconsumptionblog) správy rozhraní API, provedli jsme několik změn, abychom zjednodušili správu klíčů:
- Nejprve jsme přidali další dva obory předplatného: všechna rozhraní API a jedno rozhraní API. Rozsah předplatných již není omezen na produkt rozhraní API. Nyní je možné vytvořit klíče, které udělují přístup k rozhraní API nebo všechna rozhraní API v rámci instance správy rozhraní API, aniž byste museli vytvořit produkt a nejprve do něj přidat rozhraní API. Každá instance správy rozhraní API je nyní také dodávána s neměnným předplatným všech rozhraní API. Toto předplatné usnadňuje a zjednodušuje testování a ladění api v testovací konzole.

- Za druhé, správa rozhraní API nyní umožňuje **samostatné** odběry. Předplatná už nemusí být přidružena k vývojářskému účtu. Tato funkce je užitečná ve scénářích, jako když několik vývojářů nebo týmů sdílí předplatné.

- Nakonec vydavatelé rozhraní API teď [můžou vytvářet předplatná](api-management-howto-create-subscriptions.md) přímo na webu Azure Portal:

    ![Flexibilní předplatná](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Další kroky
Získejte další informace o správě rozhraní API:

+ Další [koncepty](api-management-terminology.md) se dozvíte ve správě rozhraní API.
+ Další informace o správě rozhraní API najdete v [našich kurzech.](import-and-publish.md)
+ Běžné otázky najdete na [stránce s nejčastějšími](api-management-faq.md) dotazy.
