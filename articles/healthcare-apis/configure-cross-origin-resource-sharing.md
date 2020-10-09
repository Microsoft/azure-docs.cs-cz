---
title: Konfigurace sdílení prostředků mezi zdroji v rozhraní Azure API pro FHIR
description: Tento článek popisuje, jak nakonfigurovat sdílení prostředků mezi zdroji v rozhraní Azure API pro FHIR.
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: dc4c034b0821f1fe5ecb940591fca77d61edc3ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871747"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>Konfigurace sdílení prostředků mezi zdroji v rozhraní Azure API pro FHIR

Rozhraní Azure API pro rychlé interoperability mezi zdravotními prostředky (FHIR) podporuje [sdílení prostředků mezi zdroji (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing). CORS umožňuje nakonfigurovat nastavení tak, aby aplikace z jedné domény (zdroje) měly přístup k prostředkům z jiné domény, která se označuje jako požadavek mezi doménami.

CORS se často používá v jednostránkové aplikaci, která musí volat rozhraní API RESTful do jiné domény.

Pokud chcete nakonfigurovat nastavení CORS v rozhraní API Azure pro FHIR, zadejte následující nastavení:

- **Origines (Access-Control-Allow-Origin)**. Seznam domén s povoleným vytvářením požadavků pro více zdrojů rozhraní API Azure pro FHIR. Každá doména (původ) musí být zadána na samostatný řádek. Můžete zadat hvězdičku (*) a povolit tak volání z libovolné domény, ale nedoporučujeme ji, protože se jedná o bezpečnostní riziko.

- **Hlavičky (Access-Control-Allow-Headers)**. Seznam hlaviček, které bude obsahovat požadavek původce Pro povolení všech hlaviček zadejte hvězdičku (*).

- **Metody (Access-Control-Allow-Methods)**. Povolené metody (PUT, GET, POST atd.) v volání rozhraní API. Zvolte **možnost Vybrat vše** pro všechny metody.

- **Maximální stáří (řízení přístupu-max. stáří)**. Hodnota v sekundách pro výsledky požadavků na kontrolu před výstupem do mezipaměti pro metody Access-Control-Allow-Headers a Access-Control-Allow-Methods.

- **Povolte přihlašovací údaje (Access-Control-Allow-Credentials)**. Požadavky CORS běžně neobsahují soubory cookie, aby nedocházelo k útokům prostřednictvím [CSRF (pro falšování požadavků mezi lokalitami)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) . Pokud vyberete toto nastavení, může být žádost vytvořena tak, aby zahrnovala přihlašovací údaje, jako jsou soubory cookie. Toto nastavení nemůžete nakonfigurovat, pokud jste již nastavili počátek pomocí hvězdičky (*).

![Nastavení sdílení prostředků mezi zdroji (CORS)](media/cors/cors.png)

>[!NOTE]
>Nemůžete zadat různá nastavení pro různé zdroje domény. Všechna nastavení (**hlavičky**, **metody**, **maximální stáří**a **povolená pověření**) platí pro všechny zdroje zadané v nastavení zdroje.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak nakonfigurovat sdílení mezi zdroji v rozhraní Azure API pro FHIR. Dále nasaďte plně spravované rozhraní Azure API pro FHIR:
 
>[!div class="nextstepaction"]
>[Nasazení Azure API for FHIR](fhir-paas-portal-quickstart.md)