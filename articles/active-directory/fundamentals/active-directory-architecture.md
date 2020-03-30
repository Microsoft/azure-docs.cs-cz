---
title: Přehled architektury – Azure Active Directory | Dokumenty společnosti Microsoft
description: Zjistěte, co je tenant Azure Active Directory a jak spravovat Azure pomocí Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 854fb4649f8c1113f20abe5807dd0ce473ba6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368064"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Co je architektura Služby Azure Active Directory?

Azure Active Directory (Azure AD) umožňuje zabezpečeně spravovat přístup k prostředkům a službám Azure pro vaše uživatele. Součástí Azure AD je kompletní sada funkcí pro správu identit. Informace o funkcích služby Azure AD najdete v tématu [Co je Azure Active Directory?](active-directory-whatis.md)

Pomocí Azure AD můžete vytvářet a spravovat uživatele a skupiny a používat sady oprávnění k povolení nebo zamítnutí přístupu k podnikovým prostředkům. Informace o správě identit najdete v tématu věnovaném [základům správy identit Azure](active-directory-whatis.md).

## <a name="azure-ad-architecture"></a>Architektura Azure AD

Geograficky distribuovaná architektura Azure AD kombinuje rozsáhlé monitorování, automatické přesměrování, převzetí služeb při selhání a možnosti obnovení, které zákazníkům poskytují dostupnost a výkon v rámci celé společnosti.

Tento článek se zabývá následujícími prvky návrhu:

*   Návrh architektury služeb
*   Škálovatelnost
*   Nepřetržitá dostupnost
*   Datová centra

### <a name="service-architecture-design"></a>Návrh architektury služeb

Nejběžnější způsob, jak vytvořit přístupný a použitelný systém bohatý na data, je prostřednictvím nezávislých stavebních bloků nebo škálovacích jednotek. Pro datovou vrstvu Azure AD se jednotky škálování nazývají *oddíly*.

Datová vrstva obsahuje několik front-endových služeb, které poskytují funkce pro čtení a zápis. Následující diagram ukazuje, jak jsou součásti oddílu s jedním adresářem doručovány v geograficky distribuovaných datových centrech.

  ![Diagram oddílů s jedním adresářem](./media/active-directory-architecture/active-directory-architecture.png)

Komponenty architektury služby Azure AD zahrnují primární repliku a sekundární repliky.

#### <a name="primary-replica"></a>Primární replika

*Primární replika* obdrží všechny *zápisy* pro oddíl, do kterého patří. Všechny operace zápisu se okamžitě replikují do sekundární repliky v jiném datovém centru. Teprve potom se volající informuje o úspěchu, aby se zajistila geograficky redundantní odolnost zápisů.

#### <a name="secondary-replicas"></a>Sekundární repliky

Všechna *čtení* adresářů jsou obsluhována ze *sekundárních replik*, které jsou v datových centrech, které jsou fyzicky umístěny v různých zeměpisných oblastech. Protože data se replikují asynchronně, existuje mnoho sekundárních replik. Čtení adresářů, jako jsou požadavky na ověření, jsou obsluhovány z datových center, které jsou blízko zákazníkům. Sekundární repliky zodpovídají za škálovatelnost čtení.

### <a name="scalability"></a>Škálovatelnost

Škálovatelnost je schopnost služby rozšířit se a plnit rostoucí požadavky na výkon. Škálovatelnosti zápisu se dosahuje dělením dat. Škálovatelnost čtení se zajišťuje replikací dat z jednoho oddílu do několika sekundárních replik distribuovaných po celém světě.

Požadavky z adresářových aplikací jsou směrovány do datového centra, ke kterému jsou fyzicky nejblíže. Zápisy se transparentně přesměrují na primární repliku, aby se zajistila konzistence čtení a zápisu. Sekundární repliky výrazně rozšiřují rozsah oddílů, protože adresáře obvykle většinu doby obsluhují čtení.

Aplikace adresáře se připojují k nejbližším datovým centrům. Toto připojení zlepšuje výkon, a proto je možné horizontální navýšení kapacity. Vzhledem k tomu, že oddíl adresáře může mít mnoho sekundárních replik, sekundární repliky mohou být umístěné blíž ke klientům adresáře. Jenom interní komponenty služby adresáře, které jsou náročné na zápis, přímo cílí na aktivní primární repliku.

### <a name="continuous-availability"></a>Nepřetržitá dostupnost

Dostupnost (nebo doba provozuschopnosti) definuje schopnost systému pracovat bez přerušení. Klíčem k vysoké dostupnosti Azure AD je, že služby můžete rychle přesunout provoz přes více geograficky distribuovaných datových center. Každé datové centrum je nezávislé, což umožňuje režimy selhání s odkondoluje. Díky tomuto návrhu vysoké dostupnosti azure ad nevyžaduje žádné prostoje pro aktivity údržby.

Návrh oddílu Azure AD je zjednodušený ve srovnání s návrhem podnikové služby AD pomocí návrhu jednoho hlavního serveru, který zahrnuje pečlivě řízený a deterministický proces převzetí služeb při selhání primární repliky.

#### <a name="fault-tolerance"></a>Odolnost proti chybám

Systém je dostupnější, pokud je odolný vůči selháním hardwaru, sítě a softwaru. Pro každý oddíl v adresáři existuje hlavní replika s vysokou dostupností: primární replika. V této replice se provádějí jenom zápisy do oddílu. Tato replika se průběžně a pečlivě monitoruje a pokud se zjistí selhání, zápisy je možné okamžitě přesunout do jiné repliky (která se stane novou primární replikou). Během převzetí služeb při selhání může dojít ke ztrátě dostupnosti zápisu, obvykle na 1 až 2 minuty. Dostupnost čtení to během této doby neovlivní.

Operace čtení (jejichž počet mnohonásobně převyšuje počet zápisů) jdou jenom do sekundárních replik. Vzhledem k tomu, že sekundární repliky jsou idempotentní, ztráta libovolné repliky v daném oddílu se dá snadno vykompenzovat přesměrováním čtení do jiné repliky, obvykle ve stejném datovém centru.

#### <a name="data-durability"></a>Odolnost dat

Zápis je trvale potvrzena alespoň dvě datová centra před tím, než je potvrzena. K tomu dochází tak, že nejprve popotvrzení zápisu na primární a okamžitě replikovat zápis alespoň do jednoho dalšího datového centra. Tato akce zápisu zajišťuje, že potenciální katastrofická ztráta datového centra hostujícího primární nemá za následek ztrátu dat.

Azure AD udržuje cíl nulové [doby obnovení (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) neztratit data o převzetí služeb při selhání. To zahrnuje:

* Vystavení tokenu a čtení adresáře
* Povolení pouze asi 5 minut RTO pro zápisy adresáře

### <a name="datacenters"></a>Datová centra

Repliky Azure AD jsou uložené v datových centrech rozmístěných po celém světě. Další informace najdete v [tématu Azure global infrastructure](https://azure.microsoft.com/global-infrastructure/).

Azure AD funguje napříč datovými centry s následujícími charakteristikami:

* Ověřování, graf a další služby služby AD jsou umístěny za službou Gateway. Gateway spravuje vyrovnávání zatížení těchto služeb. Automaticky převezme služby při selhání, pokud jsou zjištěny všechny servery není v pořádku pomocí sond transakčního stavu. Na základě těchto sond stavu brána dynamicky směruje provoz do datových center v pořádku.
* Pro *čtení*má adresář sekundární repliky a odpovídající front-endové služby v konfiguraci aktivní a aktivní pracující ve více datových centrech. V případě selhání celého datového centra bude provoz automaticky směrován do jiného datového centra.
 *U *zápisů*bude adresář přepojit primární (hlavní) repliku napříč datovými centry prostřednictvím plánovaných (nové primární je synchronizováno se starými primárními) nebo nouzovými postupy převzetí služeb při selhání. Odolnosti dat je dosaženo replikací jakékoli potvrzení alespoň dvě datová centra.

#### <a name="data-consistency"></a>Konzistence dat

Model adresáře je jedním z případných konzistence. Typický problém s distribuovanými asynchronně replikujícími systémy je, že data vrácená z "určité" repliky nemusí být aktuální. 

Azure AD poskytuje konzistenci čtení a zápisu pro aplikace cílením na sekundární repliku. Směřuje svoje zápisy do primární repliky a synchronně stahuje zápisy zpátky do sekundární repliky.

Zápisy aplikací pomocí rozhraní Microsoft Graph API Azure AD jsou abstrahovány z udržování spřažení s replikou adresáře pro konzistenci čtení a zápisu. Služba rozhraní MICROSOFT Graph API udržuje logickou relaci, která má spřažení se sekundární replikou používanou pro čtení. spřažení je zachycena v "tokenu repliky", který služba ukládá pomocí distribuované mezipaměti v sekundárním datovém centru repliky. Tento token se potom využívá pro následné operace ve stejné logické relaci. Chcete-li pokračovat v používání stejné logické relace, následné požadavky musí být směrovány do stejného datového centra Azure AD. Není možné pokračovat v logické relaci, pokud jsou požadavky klienta adresáře směrovány do více datových center Azure AD; Pokud k tomu dojde, klient má více logických relací, které mají nezávislé konzistence pro čtení a zápis.

 >[!NOTE]
 >Zápisy se okamžitě replikují do sekundární repliky, pro kterou byla provedena čtení logické relace.

#### <a name="backup-protection"></a>Ochrana záloh

Adresář místo trvalého odstranění implementuje obnovitelné odstranění. Uživatelům a tenantům to umožňuje snadné obnovení v případě náhodných odstranění ze strany zákazníka. Pokud správce klienta omylem odstraní uživatele, mohou odstraněné uživatele snadno vrátit a obnovit.

Azure AD implementuje denní zálohy všech dat, a proto může autoritativně obnovit data v případě jakýchkoli logických odstranění nebo poškození. Datová vrstva využívá kódy pro opravu chyb, takže může kontrolovat chyby a automaticky opravovat konkrétní typy chyb disku.

#### <a name="metrics-and-monitors"></a>Metriky a monitorování

Spouštění služby s vysokou dostupností vyžaduje špičkové metriky a možnosti monitorování. Azure AD průběžně analyzuje a reportuje metriky stavu klíčových služeb a kritéria úspěchu pro každou ze svých služeb. K dispozici je také nepřetržitý vývoj a ladění metrik a monitorování a upozorňování pro každý scénář, v rámci každé služby Azure AD a napříč všemi službami.

Pokud žádná služba Azure AD nefunguje podle očekávání, okamžitě se akce k obnovení funkčnosti co nejrychleji. Nejdůležitější metrika Azure AD sleduje, jak rychle živé problémy s webem lze zjistit a zmírnit pro zákazníky. Intenzivně investujeme do monitorování a výstrah, abychom minimalizovali dobu detekce (cílová hodnota TTD: < 5 minut) a provozní připravenosti s cílem minimalizovat dobu zmírnění problému (cílová hodnota TTM: < 30 minut).

#### <a name="secure-operations"></a>Bezpečný provoz

Použití provozních ovládacích prvků, jako je vícefaktorové ověřování (MFA) pro všechny operace, stejně jako auditování všech operací. Kromě toho, pomocí just-in-time zvýšení systému udělit nezbytný dočasný přístup pro všechny provozní úkol na vyžádání průběžně. Další informace najdete v tématu [Důvěryhodný cloud](https://azure.microsoft.com/support/trust-center).

## <a name="next-steps"></a>Další kroky

[Příručka pro vývojáře pro službu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop)