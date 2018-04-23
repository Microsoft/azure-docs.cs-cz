---
title: Klíčové koncepty a přehled Azure API Managementu | Dokumentace Microsoftu
description: Seznamte se s rozhraními API, produkty, rolemi, skupinami a dalšími klíčovými koncepty služby API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 0410e0176d5c853e1110fe3460c7d9314e7fd397
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
---
# <a name="what-is-api-management"></a>Co je služba API Management?

Služba API Management (APIM) pomáhá organizacím při publikování rozhraní API pro externí, partnerské a interní vývojáře, aby tak uvolnila potenciál jejich dat a služeb. Firmy po celém světě hledají způsoby, jak rozšířit svojí činnost na digitální platformě, vytvářejí nové kanály, hledají nové zákazníky a více se propojují s těmi stávajícími. Služba API Management nabízí základní možnosti pro zajištění úspěšného programu s rozhraním API prostřednictvím zapojení vývojářů, informací o podniku, analýz, zabezpečení a ochrany. S pomocí služby Azure API Management můžete vzít jakýkoli back-end a na jeho základě spustit plnohodnotný program s rozhraním API.

Tento článek obsahuje přehled běžných scénářů, které zahrnují APIM.  Také poskytuje stručný přehled hlavních komponent systému APIM. Článek dále poskytuje podrobnější přehled jednotlivých komponent.

## <a name="overview"></a>Přehled

Aby bylo možné službu API Management používat, vytvářejí správci rozhraní API. Každé rozhraní API se skládá z jedné nebo několika operací a každé rozhraní API můžete přidat do jednoho nebo více produktů. Aby mohli používat rozhraní API, přihlašují se vývojáři k odběru produktu, který obsahuje toto rozhraní API, a potom můžou volat jeho operace (v souladu s platnými zásadami využití). Mezi obvyklé scénáře patří:

* **Zabezpečení mobilní infrastruktury** pomocí přístupu s klíči rozhraní API prostřednictvím brány, ochrana před útoky DOS pomocí omezování nebo používání rozšířených zásad zabezpečení, například ověřování tokenů JWT.
* **Povolování ekosystémů partnera ISV** prostřednictvím nabídky rychlého připojení partnera skrze portál pro vývojáře a vytvořením průčelí rozhraní API za účelem oddělení od interních implementací, které ještě nejsou zralé k tomu, aby je partner využíval.
* **Spuštění interního programu s rozhraním API** prostřednictvím nabídky centralizovaného umístění, kde může organizace komunikovat o dostupnosti a nejnovějších změnách rozhraní API, přístup prostřednictvím brány na základě zabezpečeného kanálu mezi bránou rozhraní API a back-endem.

Systém se skládá z následujících součástí:

* **Brána rozhraní API** je koncovým bodem, který plní následující úkoly:
  
  * Přijímá volání rozhraní API a směruje je na váš back-end.
  * Ověřuje klíče rozhraní API, tokeny JWT, certifikáty a další přihlašovací údaje.
  * Vynucuje kvóty využití a omezení četnosti.
  * Transformuje rozhraní API za chodu beze změny kódu.
  * Ukládá odezvy back-endu do určené mezipaměti.
  * Protokoluje metadata volání pro účely analýzy.
* Web **Azure Portal** je administrativním rozhraní, ve kterém nastavujete program rozhraní API. Použijte ho k následujícím akcím:
  
  * definování nebo import schématu rozhraní API
  * balení rozhraní API do produktů
  * nastavení zásad, například kvót nebo transformací rozhraní API
  * získání přehledů z analýz
  * správa uživatelů
* **Portál pro vývojáře** funguje jako hlavní webová služba pro vývojáře, kde můžete provádět následující akce:
  
  * Číst dokumentaci k rozhraní API.
  * Vyzkoušet rozhraní API prostřednictvím interaktivní konzoly.
  * Vytvořit účet a přihlásit se k odběru za účelem získání klíčů rozhraní API.
  * Přistupovat k analýzám jejich využití.

Další informace najdete v bílé knize [Cloudová služba API Management: využití síly rozhraní API](http://j.mp/ms-apim-whitepaper) (formát PDF). Tato bílá kniha s úvodními informacemi o službě API Management vytvořená společností CITO Research obsahuje: 
 
 * Běžné požadavky a problémy rozhraní API
 * Oddělení rozhraní API a představení průčelí
 * Rychlý úvod pro vývojáře
 * Zabezpečení přístupu
 * Analýzy a metriky
 * Získání kontroly a přehledu pomocí platformy API Management
 * Použití cloudového nebo místního řešení
 * Azure API Management
 
## <a name="apis"></a>Rozhraní API a operace
Rozhraní API jsou základem instance služby API Management. Každé rozhraní API představuje sadu operací, které jsou vývojářům dostupné. Každé rozhraní API obsahuje odkaz na back-endovou službu, která implementuje rozhraní API, a jeho operace se mapují na operace implementované back-endovou službou. Operace ve službě API Management jsou vysoce konfigurovatelné a umožňují kontrolu nad mapováním adres URL, parametry dotazů a cest, obsahem požadavků a odezev a ukládáním operací do mezipaměti. Na úrovni rozhraní API nebo jednotlivé operace můžete implementovat také zásady kvót, omezení četnosti nebo omezení IP.

Další informace najdete v článcích [Vytvoření rozhraní API][How to create APIs] a [Přidání operací do rozhraní API][How to add operations to an API].

## <a name="products"></a> Produkty
Rozhraní API se k vývojářům dostávají prostřednictvím produktů. Produkty v API Management mají jedno nebo několik rozhraní API a mají nakonfigurovaný název, popis a podmínky použití. Produkty můžou být **otevřené** nebo **chráněné**. V případě chráněných produktů se musíte nejdřív přihlásit k jejich odběru a až potom je můžete používat. Otevřené produkty můžete používat bez předplatného. Jakmile je produkt připravený k použití pro vývojáře, můžete ho publikovat. Publikovaný produkt si můžou vývojáři zobrazovat (v případě chráněných produktů je podmínkou předplatné). Schválení předplatného se konfiguruje na úrovni produktu. Buď se může vyžadovat schválení správce, nebo se může schvalovat automaticky.

Ke správě viditelnosti produktů pro vývojáře se používají skupiny. Produkty udělují viditelnost skupinám a vývojáři můžou zobrazovat a odebírat produkty, které jsou viditelné pro skupinu, do které patří. 

## <a name="groups"></a> Skupiny
Ke správě viditelnosti produktů pro vývojáře se používají skupiny. Služba API Management má následující neměnné systémové skupiny:

* **Správci** – členy této skupiny jsou správci předplatného Azure. Správci spravují instance služby API Management, vytváření rozhraní API, operace a produkty, které používají vývojáři.
* **Vývojáři** – do této skupiny patří ověření uživatelé portálu pro vývojáře. Vývojáři jsou zákazníci, kteří vytvářejí aplikace pomocí vašich rozhraní API. Vývojáři mají přístup k portálu pro vývojáře a vytvářejí aplikace, které volají operace rozhraní API.
* **Hosté** – do této skupiny patří neověření uživatelé portálu pro vývojáře, například potenciální zákazníci, kteří navštěvují portál pro vývojáře v instanci služby API Management. Můžete jim udělit omezený přístup jenom ke čtení, například k zobrazení rozhraní API bez možnosti jeho volání.

Kromě těchto systémových skupin můžou správci vytvářet vlastní skupiny nebo [využívat externí skupiny v přidružených klientech služby Azure Active Directory](api-management-howto-aad.md). Vlastní a externí skupiny můžete používat společně se systémovými skupinami, a nastavovat tak vývojářům viditelnost produktů s rozhraním API a přístup k nim. Můžete například vytvořit jednu vlastní skupinu pro vývojáře spojené s konkrétní partnerskou organizací a povolit jim přístup k rozhraním API z produktu, který obsahuje jenom příslušná rozhraní API. Uživatel může být členem několika skupin.

Další informace najdete v článku [Vytvoření a používání skupin][How to create and use groups].

## <a name="developers"></a> Vývojáři
Vývojáři představují uživatelské účty v instanci služby API Management. Vývojáře můžou vytvořit nebo pozvat správci, nebo se můžou sami zaregistrovat na [portálu pro vývojáře][Developer portal]. Každý vývojář je členem jedné nebo několika skupin a může se přihlásit k odběru produktů, které jsou pro tyto skupiny viditelné.

Když se vývojáři přihlásí k odběru produktu, získají primární a sekundární klíč produktu. Tento klíč se používá při volání do rozhraní API produktu.

Další informace najdete v článcích o [vytváření a zvaní vývojářů][How to create or invite developers] a [přidružení skupin k vývojářům][How to associate groups with developers].

## <a name="policies"></a> Zásady
Zásady jsou vynikající funkcí služby API Management, která webu Azure Portal umožňuje měnit chování rozhraní API prostřednictvím konfigurace. Zásady představují kolekci příkazů, které se postupně provádí na základě požadavku nebo odezvy z rozhraní API. Mezi oblíbené příkazy patří převod formátu XML do formátu JSON a omezení četnosti volání, kterým omezíte počet příchozích volání od vývojáře. K dispozici je i mnoho dalších zásad.

Výrazy zásad můžete použít jako hodnoty atributů nebo textové hodnoty v libovolných zásadách API Management (pokud zásady neurčí jinak). Některé zásady, například [řízení toku](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) a [nastavená proměnná](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable), jsou založené na výrazech zásad. Další informace najdete v článcích [Pokročilé zásady](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies) a [Výrazy zásad](https://msdn.microsoft.com/library/azure/dn910913.aspx).


Úplný seznam zásad služby API Management najdete v [referenční příručce o zásadách][Policy reference]. Další informace o používání a konfiguraci zásad najdete v článku [Zásady služby API Management][API Management policies]. Kurz týkající se vytváření produktu se zásadami kvót a omezování četnosti najdete v článku [Vytvoření a konfigurace pokročilých nastavení produktu][How create and configure advanced product settings].


## <a name="developer-portal"></a> Portál pro vývojáře
Portál pro vývojáře je místo, na kterém se můžou vývojáři dozvědět o vašich rozhraních API, zobrazit a volat operace a přihlásit se k odběru produktů. Potenciální zákazníci můžou navštívit portál pro vývojáře, zobrazovat rozhraní API a operace a zaregistrovat se. Adresa URL portálu pro vývojáře je umístěná na řídicím panelu na webu Azure Portal vaší instance služby API Management.

Vzhled portálu pro vývojáře můžete přizpůsobit přidáním vlastního obsahu, přizpůsobením stylů a přidáním brandingu.

## <a name="api-management-and-the-api-economy"></a>Služba API Management a ekonomika rozhraní API

Pokud se chcete o službě API Management dozvědět víc, podívejte se na následující prezentaci z konference Microsoft Ignite 2017.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2186/player]
> 
> 

## <a name="next-steps"></a>Další kroky

Dokončete následující rychlý start a začněte používat Azure API Management:

> [!div class="nextstepaction"]
> [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md)

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: api-management-howto-create-apis.md
[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: transform-api.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: api-management-policy-reference.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: get-started-create-service-instance.md




