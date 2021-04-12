---
title: Přehled architektury – Azure Active Directory | Microsoft Docs
description: Přečtěte si, co je tenant Azure Active Directory a jak spravovat Azure pomocí Azure Active Directory.
services: active-directory
author: ajburnle
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
ms.openlocfilehash: 476cf8013f5dc8b5d54efb573cf305d81fc690b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996708"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Co je architektura Azure Active Directory?

Azure Active Directory (Azure AD) umožňuje zabezpečeně spravovat přístup k prostředkům a službám Azure pro vaše uživatele. Součástí Azure AD je kompletní sada funkcí pro správu identit. Informace o funkcích služby Azure AD najdete v tématu [Co je Azure Active Directory?](active-directory-whatis.md)

Pomocí Azure AD můžete vytvářet a spravovat uživatele a skupiny a používat sady oprávnění k povolení nebo zamítnutí přístupu k podnikovým prostředkům. Informace o správě identit najdete v tématu věnovaném [základům správy identit Azure](active-directory-whatis.md).

## <a name="azure-ad-architecture"></a>Architektura Azure AD

Geograficky distribuovaná architektura Azure AD kombinuje rozsáhlé možnosti monitorování, automatizovaného přesměrování, převzetí služeb při selhání a obnovení, které zákazníkům zajišťuje dostupnost a výkon v celé společnosti.

Tento článek se zabývá následujícími prvky návrhu:

*   Návrh architektury služeb
*   Škálovatelnost
*   Nepřetržitá dostupnost
*   Datová centra

### <a name="service-architecture-design"></a>Návrh architektury služeb

Nejběžnější způsob, jak vytvořit přístupný a použitelný systém bohatý na data, je prostřednictvím nezávislých stavebních bloků nebo jednotek škálování. Pro datovou vrstvu Azure AD se jednotky škálování označují jako *oddíly*.

Datová vrstva obsahuje několik front-endových služeb, které poskytují funkce pro čtení a zápis. Následující diagram znázorňuje, jak se součásti oddílu s jedním adresářem dodávají v rámci geograficky distribuovaných datových center.

  ![Diagram oddílů s jedním adresářem](./media/active-directory-architecture/active-directory-architecture.png)

Komponenty architektury služby Azure AD zahrnují primární repliku a sekundární repliky.

#### <a name="primary-replica"></a>Primární replika

*Primární replika* obdrží všechny *zápisy* pro oddíl, do kterého patří. Všechny operace zápisu se okamžitě replikují do sekundární repliky v jiném datovém centru. Teprve potom se volající informuje o úspěchu, aby se zajistila geograficky redundantní odolnost zápisů.

#### <a name="secondary-replicas"></a>Sekundární repliky

Všechny *čtení* adresáře se obsluhují ze *sekundárních replik*, které se nacházejí v datových centrech, které jsou fyzicky umístěné v různých geografických oblastech. Protože data se replikují asynchronně, existuje mnoho sekundárních replik. Čtení adresáře, například žádosti o ověření, jsou obsluhovaná z datových center, která se blíží zákazníkům. Sekundární repliky zodpovídají za škálovatelnost čtení.

### <a name="scalability"></a>Škálovatelnost

Škálovatelnost je schopnost služby rozšířit se a plnit rostoucí požadavky na výkon. Škálovatelnosti zápisu se dosahuje dělením dat. Škálovatelnost čtení se zajišťuje replikací dat z jednoho oddílu do několika sekundárních replik distribuovaných po celém světě.

Požadavky z adresářových aplikací jsou směrovány do datového centra, které jsou fyzicky nejblíže. Zápisy se transparentně přesměrují na primární repliku, aby se zajistila konzistence čtení a zápisu. Sekundární repliky výrazně rozšiřují rozsah oddílů, protože adresáře obvykle většinu doby obsluhují čtení.

Aplikace adresáře se připojují k nejbližším datovým centrům. Toto připojení zvyšuje výkon, a proto je možné horizontální navýšení kapacity. Vzhledem k tomu, že oddíl adresáře může mít mnoho sekundárních replik, sekundární repliky mohou být umístěné blíž ke klientům adresáře. Jenom interní komponenty služby adresáře, které jsou náročné na zápis, přímo cílí na aktivní primární repliku.

### <a name="continuous-availability"></a>Nepřetržitá dostupnost

Dostupnost (nebo doba provozuschopnosti) definuje schopnost systému pracovat bez přerušení. Klíčem k zajištění vysoké dostupnosti služby Azure AD je, že služby můžou rychle posunout provoz napříč několika geograficky rozloženými datacentry. Každé datové centrum je nezávislé, což umožňuje nerelační režimy selhání. V rámci tohoto návrhu vysoké dostupnosti služba Azure AD nevyžaduje žádné výpadky aktivit údržby.

Návrh oddílů Azure AD se v porovnání s návrhem podnikové sítě zjednodušuje pomocí návrhu s jedním hlavním serverem, který zahrnuje pečlivě Orchestrované a deterministické procesy převzetí služeb při selhání primární repliky.

#### <a name="fault-tolerance"></a>Odolnost proti chybám

Systém je dostupnější, pokud je odolný vůči selháním hardwaru, sítě a softwaru. Pro každý oddíl v adresáři existuje hlavní replika s vysokou dostupností: primární replika. V této replice se provádějí jenom zápisy do oddílu. Tato replika se průběžně a pečlivě monitoruje a pokud se zjistí selhání, zápisy je možné okamžitě přesunout do jiné repliky (která se stane novou primární replikou). Během převzetí služeb při selhání může dojít ke ztrátě dostupnosti zápisu, obvykle na 1 až 2 minuty. Dostupnost čtení to během této doby neovlivní.

Operace čtení (jejichž počet mnohonásobně převyšuje počet zápisů) jdou jenom do sekundárních replik. Vzhledem k tomu, že sekundární repliky jsou idempotentní, ztráta libovolné repliky v daném oddílu se dá snadno vykompenzovat přesměrováním čtení do jiné repliky, obvykle ve stejném datovém centru.

#### <a name="data-durability"></a>Odolnost dat

Zápis se trvale před potvrzením do alespoň dvou datových center. K tomu dochází, když nejdřív potvrdíte zápis na primárním počítači a pak hned znovu replikujte zápis do aspoň jednoho jiného datového centra. Tato akce zápisu zajišťuje, že potenciální závažná ztráta datacentra hostujícího primární službu nevede ke ztrátě dat.

Azure AD udržuje [objektivní čas obnovení (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) , který neztratí data při převzetí služeb při selhání. Sem patří:

* Vystavení tokenu a čtení adresáře
* Povoluje se jenom asi 5 minut RTO pro zápisy adresářů.

### <a name="datacenters"></a>Datová centra

Repliky Azure AD jsou uložené v datových centrech rozmístěných po celém světě. Další informace najdete v tématu [globální infrastruktura Azure](https://azure.microsoft.com/global-infrastructure/).

Azure AD funguje v datových centrech s následujícími charakteristikami:

* Ověřování, grafy a další služby AD jsou umístěné za službou brány. Gateway spravuje vyrovnávání zatížení těchto služeb. Dojde k automatickému převzetí služeb při selhání, pokud se zjistí případné nestavové servery pomocí transakčních sond stavu. Na základě těchto sond stavu brána dynamicky směruje provoz na datová centra v pořádku.
* V případě *čtení* má adresář sekundární repliky a odpovídající front-endové služby v konfiguraci aktivní-aktivní v několika datových centrech. V případě selhání celého datového centra se provoz automaticky směruje na jiné datové centrum.
 * V případě *zápisů* se adresář převezme primární (hlavní) replika napříč datovými centry prostřednictvím plánu (nová primární databáze je synchronizovaná se starými primárními) nebo postupy nouzového převzetí služeb při selhání. Odolnost dat se dosahuje replikací všech potvrzení do aspoň dvou datových center.

#### <a name="data-consistency"></a>Konzistence dat

Adresářový model je jedním z případných konečného výsledku. Jedním z typických problémů s distribuovanými asynchronně replikovanými systémy je to, že data vrácená z konkrétní repliky nemusí být aktuální. 

Azure AD poskytuje konzistenci čtení a zápisu pro aplikace cílením na sekundární repliku. Směřuje svoje zápisy do primární repliky a synchronně stahuje zápisy zpátky do sekundární repliky.

Zápisy aplikací pomocí rozhraní Microsoft Graph API služby Azure AD jsou abstrakcí z udržování spřažení s replikou adresáře pro zajištění konzistence čtení a zápisu. Služba Microsoft Graph API udržuje logickou relaci, která má spřažení se sekundární replikou používanou pro čtení. spřažení je zachyceno v "tokenu repliky", který služba ukládá do mezipaměti pomocí distribuované mezipaměti v datacentru sekundárního repliky. Tento token se potom využívá pro následné operace ve stejné logické relaci. Aby bylo možné dál používat stejnou logickou relaci, následné požadavky musí být směrovány do stejného datacentra Azure AD. Pokud se požadavky na klienta adresáře směrují do více datových center Azure AD, není možné pokračovat v logické relaci. Pokud k tomu dojde, bude mít klient několik logických relací, které mají nezávislou konečného výsledku pro čtení a zápis.

 >[!NOTE]
 >Zápisy se okamžitě replikují do sekundární repliky, pro kterou byla provedena čtení logické relace.

#### <a name="backup-protection"></a>Ochrana záloh

Adresář místo trvalého odstranění implementuje obnovitelné odstranění. Uživatelům a tenantům to umožňuje snadné obnovení v případě náhodných odstranění ze strany zákazníka. Pokud správce tenanta omylem odstraní uživatele, může snadno zrušit a obnovit odstraněné uživatele.

Azure AD implementuje denní zálohy všech dat, a proto může autoritativně obnovit data v případě jakýchkoli logických odstranění nebo poškození. Datová vrstva využívá chyby oprav kódů, takže může kontrolovat chyby a automaticky opravovat konkrétní typy chyb na disku.

#### <a name="metrics-and-monitors"></a>Metriky a monitorování

Spouštění služby s vysokou dostupností vyžaduje špičkové metriky a možnosti monitorování. Azure AD průběžně analyzuje a reportuje metriky stavu klíčových služeb a kritéria úspěchu pro každou ze svých služeb. Mezi jednotlivými službami Azure AD a všemi službami je také průběžný vývoj a ladění metrik a monitorování a upozorňování pro každý scénář.

Pokud některá služba Azure AD nefunguje podle očekávání, okamžitě se spustí funkce pro obnovení funkcí co nejrychleji. Nejdůležitější metrikami služby Azure AD je, jak rychle zjistit a zmírnit problémy s živým webem pro zákazníky. Intenzivně investujeme do monitorování a výstrah, abychom minimalizovali dobu detekce (cílová hodnota TTD: < 5 minut) a provozní připravenosti s cílem minimalizovat dobu zmírnění problému (cílová hodnota TTM: < 30 minut).

#### <a name="secure-operations"></a>Bezpečný provoz

Použití operačních ovládacích prvků, jako je Multi-Factor Authentication (MFA) pro jakoukoli operaci, a také auditování všech operací. Kromě toho je možné pomocí systému řízení přihlašování za běhu udělit nezbytný dočasný přístup pro všechny operační úkoly na vyžádání průběžně. Další informace najdete v tématu [Důvěryhodný cloud](https://azure.microsoft.com/support/trust-center).

## <a name="next-steps"></a>Další kroky

[Příručka pro vývojáře pro službu Azure Active Directory](../develop/index.yml)