---
title: Rozhraní API pro plnění SaaS na komerčním tržišti Microsoftu
description: Úvod k rozhraním API pro splnění, které vám umožní integrovat nabídky SaaS do Microsoft AppSource a Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2e6381afb19018822f6f37171a5ca4b3d929b42e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88037520"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>Rozhraní API pro plnění SaaS na komerčním tržišti Microsoftu

Rozhraní API pro splnění SaaS umožňují vydavatelům, kteří se označují také jako nezávislí výrobci softwaru (ISV), publikovat a prodávat své SaaS aplikace v Microsoft AppSource, Azure Marketplace a Azure Portal. Tato rozhraní API umožňují aplikacím ISV zapojit se do všech kanálů s povoleným obchodováním: přímé, partner – LED (prodejce) a indikátor LED v poli.  Integrace s těmito rozhraními API je požadavek na vytvoření a publikování SaaS nabídky v partnerském centru.

Nezávislí výrobci softwaru musí implementovat následující toky API přidáním do jejich kódu služby SaaS, aby zachovaly stejný stav předplatného pro nezávislé výrobce softwaru i společnost Microsoft:

* Tok úvodní stránky: Společnost Microsoft oznámí vydavateli, že nabídka SaaS byla zakoupena zákazníkem na webu Marketplace.
* Tok aktivace: Vydavatel oznamuje společnosti Microsoft, že nově koupený účet SaaS byl nakonfigurován na straně vydavatele.
* Aktualizovat tok: Změna zakoupeného plánu nebo počet zakoupených stanic.
* Pozastavit a obnovit tok: pozastavení zakoupené nabídky SaaS v případě, že platební metoda zákazníka již není platná. Po vyřešení problému s metodou platby je možné nabídku pozastavené nabídky obnovit.
* Toky Webhooku: Microsoft pošle vydavateli informace o změnách předplatného SaaS a zrušení aktivované zákazníkem ze strany Microsoftu.

Pro zrušení zakoupeného předplatného SaaS je integrace volitelná, protože ji zákazník může provést na straně Microsoftu.

Správná integrace s rozhraními API pro plnění SaaS jsou zásadní pro zajištění, že

* koncovým zákazníkům, kteří si zakoupili nabídku SaaS vydavatele, se společnost Microsoft účtuje správně.
* koncoví zákazníci získávají správné uživatelské prostředí při nákupu, konfiguraci, používání a správě předplatných SaaS zakoupených na webu Marketplace.

Tato rozhraní API umožňují, aby se nabídky vydavatele účastnily všech kanálů s povoleným obchodováním:

* přímé
* partner – LED (prodejce, CSP)
* Indikátor LED pole

Ve scénáři prodejce (CSP) si poskytovatel CSP koupí nabídku SaaS jménem koncového zákazníka. Očekává se, že zákazník použije nabídku SaaS, ale CSP je entita, která provede následující:

* fakturace zákazníka
* Změna plánů předplatného/objemu koupených sedadel
* rušení předplatných

Vydavatel není vyžadován pro implementaci jakýchkoli toků volání rozhraní API v tomto scénáři jinak.

Další informace o CSP najdete v tématu https://partner.microsoft.com/licensing .

>[!Warning]
>Aktuální verze tohoto rozhraní API je verze 2, která by se měla používat pro všechny nové nabídky SaaS. Verze 1 rozhraní API je zastaralá a udržuje se tak, aby podporovala stávající nabídky.

>[!Note]
>Rozhraní API pro plnění SaaS jsou určena pouze pro volání ze služby back-endu vydavatele. Integrace s rozhraními API přímo z webové stránky vydavatele není podporována. Měl by se použít jenom tok ověřování služby na službu.

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, zaregistrujte svou aplikaci SaaS do [Azure Portal](https://ms.portal.azure.com) , jak je vysvětleno v tématu [Registrace aplikace Azure AD](./pc-saas-registration.md).  Potom pro vývoj použijte nejaktuálnější verzi tohoto rozhraní: [SaaS API pro splnění verze 2](./pc-saas-fulfillment-api-v2.md).
