---
title: Ochrana heslem v Azure Active Directory
description: Přečtěte si, jak dynamicky zakázat slabé heslo z vašeho prostředí pomocí Azure Active Directory ochrany heslem.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4da1066166a3384ffb5f0f94599452829faed1b
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356582"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>Eliminujte chybná hesla pomocí Azure Active Directory ochrany heslem.

Spousta pokynů k zabezpečení doporučuje, abyste nepoužívali stejné heslo na více místech, aby se zajistila složitá a aby nedocházelo k jednoduchým heslům, jako je *Password123*. Uživatelům můžete poskytnout [pokyny, jak zvolit hesla](https://www.microsoft.com/research/publication/password-guidance), ale slabá nebo nezabezpečená hesla se často používají. Ochrana heslem Azure AD detekuje a blokuje známá slabá hesla a jejich varianty a může také blokovat další slabé výrazy, které jsou specifické pro vaši organizaci.

Díky ochraně hesel Azure AD se výchozí globální seznamy zakázaných hesel automaticky aplikují na všechny uživatele v tenantovi Azure AD. Pro podporu vlastních obchodních potřeb a zabezpečení můžete definovat položky v seznamu vlastního zakázaného hesla. Když uživatelé mění nebo resetují hesla, jsou tyto seznamy zakázaných hesel zaškrtnuté, aby vynutily používání silných hesel.

Měli byste používat další funkce, jako je [Azure Multi-Factor Authentication](concept-mfa-howitworks.md). nespoléhá se jenom na silná hesla vynucená ochranou hesel Azure AD. Další informace o použití několika vrstev zabezpečení pro přihlašovací události najdete v tématu o tom, co je [to $Word PA](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

> [!IMPORTANT]
> Tento koncepční článek vysvětluje správce, jak funguje ochrana heslem Azure AD. Pokud už jste koncoví uživatelé zaregistrovali pro Samoobslužné resetování hesla a potřebujete se zpátky do svého účtu, pokračujte na [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Pokud váš IT tým nepovolil možnost resetovat si vlastní heslo, obraťte se na helpdesk a získáte další pomoc.

## <a name="global-banned-password-list"></a>Globální seznam zakázaných hesel

Tým Azure AD Identity Protection průběžně analyzuje data telemetrie zabezpečení Azure AD, která hledají běžně používaná slabá nebo ohrožená hesla. Konkrétně analýza vyhledá základní výrazy, které se často používají jako základ pro slabá hesla. Po nalezení slabých podmínek se přidá do *seznamu globálních zakázaných hesel*. Obsah seznamu globálních zakázaných hesel není založený na žádném externím zdroji dat, ale na výsledcích telemetrie a analýz zabezpečení Azure AD.

Při změně nebo resetování hesla pro libovolného uživatele v tenantovi služby Azure AD se používá aktuální verze seznamu globálních zakázaných hesel k ověření síly hesla. Tato kontrola ověření má za následek silnější hesla pro všechny zákazníky Azure AD.

Globální seznam zakázaných hesel se automaticky použije pro všechny uživatele v tenantovi Azure AD. Není k dispozici žádná možnost povolit ani konfigurovat a nelze ji zakázat. Tento globální seznam zakázaných hesel se aplikuje uživatelům při změně nebo resetování vlastního hesla prostřednictvím služby Azure AD.

> [!NOTE]
> Internetoví – zločinci používají podobné strategie ve svých útocích k identifikaci běžných slabých hesel a variací. Pro zvýšení zabezpečení nepublikuje Microsoft obsah seznamu globálních zakázaných hesel.

## <a name="custom-banned-password-list"></a>Vlastní seznam zakázaných hesel

Některé organizace chtějí zvýšit zabezpečení a přidat vlastní vlastní nastavení nad seznam globálních zakázaných hesel. Pokud chcete přidat vlastní položky, můžete použít *vlastní seznam zakázaných hesel*. Výrazy přidané do seznamu vlastních zakázaných hesel by měly být zaměřené na konkrétní organizační výrazy, například v následujících příkladech:

- Názvy značek
- Názvy produktů
- Umístění, jako je například ústředí společnosti
- Interní výrazy specifické pro společnost
- Zkratky, které mají konkrétní význam společnosti

Pokud jsou výrazy přidány do seznamu vlastních zakázaných hesel, budou zkombinovány s podmínkami v seznamu globální seznam zakázaných hesel. Události změny a resetování hesla se pak ověřují na kombinované sadě seznamů zakázaných hesel.

> [!NOTE]
> Vlastní seznam zakázaných hesel je omezený na maximum 1000 podmínek. Není určený pro blokování extrémně rozsáhlých seznamů hesel.
>
> Abyste mohli plně využít výhody vlastního seznamu zakázaných hesel, nejdřív pochopíte, [jak jsou hesla vyhodnocována](#how-are-passwords-evaluated) před přidáním podmínek do vlastního seznamu zakázaných adres. Tento přístup umožňuje efektivně detekovat a blokovat velký počet slabých hesel a jejich variant.

![Úprava vlastního seznamu zakázaných hesel v části metody ověřování](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

Pojďme se považovat za zákazníka s názvem *Contoso*. Společnost je založena na Londýně a vytvoří produkt s názvem *widget*. Pro tento příklad zákazníka by byl wasteful a méně zabezpečený, aby se pokusil zablokovat konkrétní variace těchto podmínek, například následující:

- Contoso! 1
- "Contoso@London"
- "ContosoWidget"
- "! Contoso
- "LondonHQ"

Místo toho je mnohem efektivnější a bezpečné zablokovat jenom základní Klíčové výrazy, například následující příklady:

- "Contoso"
- London
- Pomůck

Algoritmus ověřování hesla pak automaticky blokuje slabé varianty a kombinace.

Pokud chcete začít používat vlastní seznam zakázaných hesel, dokončete následující kurz:

> [!div class="nextstepaction"]
> [Kurz: Konfigurace vlastních zakázaných hesel](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Útoky prostřednictvím spreje hesla a seznamy hesel pro zneužití třetích stran

Ochrana heslem Azure AD pomáhá chránit proti útokům prostřednictvím postřiku hesla. Většina útoků na postřik hesla se nepokouší o útok na určitý účet více než několikrát. Toto chování by zvýšilo pravděpodobnost detekce, a to buď prostřednictvím uzamčení účtu, nebo jiným způsobem.

Místo toho většina útoků proti rozstřiku hesla odesílá jenom malý počet známých slabých hesel proti každému účtu v podniku. Tato technika umožňuje útočníkovi rychle vyhledat snadno narušený účet a vyhnout se potenciálním prahům při detekci.

Azure AD Password Protection efektivně blokuje všechna známá slabá hesla, která jsou pravděpodobně používána v útocích proti rozstřiku hesla. Tato ochrana je založená na skutečných datech telemetrie zabezpečení z Azure AD a vytváří globální seznam zakázaných hesel.

Existují některé weby třetích stran, které vyčíslují miliony hesel, které byly ohroženy předchozími veřejně známými narušeními zabezpečení. Je běžné, že produkty pro ověřování hesel třetích stran vycházejí z porovnání hrubou silou s těmito miliony hesel. Nicméně tyto techniky nejsou nejlepším způsobem, jak zlepšit celkovou sílu hesla, a to s využitím typických strategií používaných útočníky pro postřik hesel.

> [!NOTE]
> Globální seznam zakázaných hesel není založený na žádných zdrojích dat třetích stran, včetně seznamů napadených hesel.

I když je globální seznam zakázaných dat malý v porovnání s některými hromadnými seznamy třetích stran, je z reálné telemetrie zabezpečení na skutečné útoky proti heslům z reálného světa. Tento přístup zlepšuje celkové zabezpečení a efektivitu a algoritmus ověřování hesla také používá inteligentní techniky pro přibližné rozhledání. V důsledku toho Azure AD Password Protection efektivně detekuje a blokuje miliony nejběžnějších slabých hesel, které se ve vašem podniku používají.

## <a name="on-premises-hybrid-scenarios"></a>Místní hybridní scénáře

Řada organizací má model hybridní identity, který zahrnuje místní prostředí Active Directory Domain Services (služba AD DS). K rozšiřování výhod zabezpečení ochrany heslem Azure AD do prostředí služba AD DS můžete komponenty nainstalovat na místní servery. Tito agenti vyžadují pro místní služba AD DS prostředí události změny hesla, aby vyhověly stejným zásadám hesel jako v Azure AD.

Další informace najdete v tématu věnovaném [vymáhání ochrany heslem Azure AD pro služba AD DS](concept-password-ban-bad-on-premises.md).

## <a name="how-are-passwords-evaluated"></a>Jak se vyhodnocují hesla

Když uživatel změní nebo obnoví heslo, bude nové heslo kontrolováno o sílu a složitosti tím, že je ověří podle kombinovaného seznamu podmínek z globálních a vlastních seznamů zakázaných hesel.

I v případě, že heslo uživatele obsahuje zakázané heslo, může být přijato heslo, pokud je celkové heslo jinak dostatečně silné. Nově konfigurované heslo provede následující kroky k vyhodnocení jeho celkové síly, aby bylo možné určit, jestli se má přijmout nebo odmítnout:

### <a name="step-1-normalization"></a>Krok 1: normalizace

Nové heslo se nejprve prochází procesem normalizace. Tato technika umožňuje mapovat malou sadu zakázaných hesel na mnohem větší sadu potenciálně slabých hesel.

Normalizace má následující dvě části:

* Všechna velká písmena jsou změněna na malá písmena.
* Pak jsou provedeny běžné náhrady znaků, například v následujícím příkladu:

   | Původní dopis | Nahrazené písmeno |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | s                  |
   | \@              | pro                  |

Uvažujte následující příklad:

* Heslo "prázdné" je zakázáno.
* Uživatel se pokusí změnit heslo na " Bl@nK ".
* I když Bl@nk není zakázaná, proces normalizace toto heslo převede na "prázdné".
* Heslo by se zamítlo.

### <a name="step-2-check-if-password-is-considered-banned"></a>Krok 2: ověření, jestli se heslo považuje za zakázané

Heslo je pak zkontrolováno pro jiné vyhovující chování a je vygenerováno skóre. Tento konečný výsledek určuje, jestli je žádost o změnu hesla přijatá nebo zamítnutá.

#### <a name="fuzzy-matching-behavior"></a>Chování při přibližném porovnání

V normalizovaném hesle se používá přibližná shoda k identifikaci, zda obsahuje heslo nalezené v globálním nebo vlastním seznamu zakázaných hesel. Shodný proces je založen na jedné z úprav vzdálenosti (1) porovnání.

Uvažujte následující příklad:

* Heslo "ABCDEF" je zakázáno.
* Uživatel se pokusí změnit heslo na jednu z následujících možností:

   * ' abcdeg ' – *poslední znak se změnil z ' f ' na ' g '*
   * ' abcdefg '- *' g ' připojeno k elementu end*
   * ' abcde ' – *koncový znak ' f ' byl odstraněn z elementu end*

* Každé z výše uvedených hesel se konkrétně neshoduje se zakázaným heslem "ABCDEF".

    Vzhledem k tomu, že každý příklad je v rámci úpravy vzdálenosti 1 zakázaného termínu "ABCDEF", jsou všechny považovány za shodné s "ABCDEF".
* Tato hesla by byla zamítnuta.

#### <a name="substring-matching-on-specific-terms"></a>Shoda podřetězců (za určitých podmínek)

Pro porovnání podřetězců se používá normalizované heslo ke kontrole křestní jméno a příjmení uživatele a také název tenanta. Shoda s názvem tenanta není při ověřování hesel na služba AD DS řadiči domény pro místní hybridní scénáře prováděna.

> [!IMPORTANT]
> Shoda podřetězců je vynutila pouze pro názvy a jiné výrazy, které mají alespoň čtyři znaky dlouhé.

Uvažujte následující příklad:

* Uživatel s názvem cyklické dotazování, který chce resetovat heslo na "p0LL23fb".
* Po normalizaci se toto heslo stane "poll23fb".
* Shoda podřetězců najde, že heslo obsahuje křestní jméno uživatele "dotaz".
* I když "poll23fb" nebylo konkrétně výslovně nastavené na seznam zakázaných hesel, v hesle byl nalezen řetězec shodný s řetězcem.
* Heslo by se zamítlo.

#### <a name="score-calculation"></a>Výpočet skóre

Dalším krokem je identifikace všech instancí zakázaných hesel v normalizovaném novém heslu uživatele. Body jsou přiřazeny na základě následujících kritérií:

1. Každé zakázané heslo, které se nachází v uživatelském hesle, je přiděleno na jeden bod.
1. Každý zbývající jedinečný znak je dán jedním bodem.
1. Heslo musí mít aspoň pět (5) bodů, které se mají přijmout.

V dalších dvou ukázkových scénářích používá contoso používání ochrany heslem Azure AD a má ve svém vlastním seznamu zakázaných hesel "contoso". Pojďme také předpokládat, že je "prázdná" v globálním seznamu.

V následujícím ukázkovém scénáři uživatel změní heslo na "C0ntos0Blank12":

* Po normalizaci se toto heslo bude "contosoblank12".
* Odpovídající proces zjistí, že toto heslo obsahuje dvě zakázaná hesla: contoso a blank.
* K tomuto heslu se pak dostanou následující skóre:

    *[contoso] + [prázdné] + [1] + [2] = 4 body*

* Jelikož je toto heslo pod pěti (5) body, je odmítnuto.

Pojďme se podívat na trochu odlišný příklad, který ukazuje, jak může další složitost hesla vytvořit požadovaný počet bodů, které se mají přijmout. V následujícím ukázkovém scénáři uživatel změní heslo na " ContoS0Bl@nkf9 !":

* Po normalizaci se toto heslo bude "contosoblankf9!".
* Odpovídající proces zjistí, že toto heslo obsahuje dvě zakázaná hesla: contoso a blank.
* K tomuto heslu se pak dostanou následující skóre:

    *[contoso] + [prázdné] + [f] + [9] + [!] = 5 bodů*

* Protože toto heslo je aspoň pět (5) bodů, je přijaté.

> [!IMPORTANT]
> Algoritmus zakázaného hesla spolu s globálním seznamem zakázaných hesel může a v Azure kdykoli měnit, a to na základě průběžné analýzy zabezpečení a výzkumu.
>
> Pro místní službu agenta řadiče domény v hybridních scénářích se aktualizované algoritmy projeví až po upgradu softwaru agenta DC.

## <a name="what-do-users-see"></a>Co vidí uživatelé

Když se uživatel pokusí resetovat heslo na něco, co by bylo zakázané, zobrazí se tato chybová zpráva:

*"Heslo bohužel obsahuje slovo, frázi nebo vzor, který umožňuje snadnou možnost uhodnout heslo. Zkuste to prosím znovu s jiným heslem. "*

## <a name="license-requirements"></a>Licenční požadavky

| Uživatelé | Ochrana heslem Azure AD s globálním seznamem zakázaných hesel | Ochrana heslem Azure AD s vlastním seznamem zakázaných hesel|
|-------------------------------------------|---------------------------|---------------------------|
| Jenom cloudové uživatele                          | Azure AD Free             | Azure AD Premium P1 nebo P2 |
| Uživatelé synchronizovaný z místních služba AD DS | Azure AD Premium P1 nebo P2 | Azure AD Premium P1 nebo P2 |

> [!NOTE]
> Místní služba AD DS uživatelům, kteří nejsou synchronizovaný se službou Azure AD, můžou také těžit z ochrany hesel Azure AD na základě stávajícího licencování pro synchronizované uživatele.

Další informace o licencování, včetně nákladů, najdete na [webu Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat vlastní seznam zakázaných hesel, dokončete následující kurz:

> [!div class="nextstepaction"]
> [Kurz: Konfigurace vlastních zakázaných hesel](tutorial-configure-custom-password-protection.md)

Můžete také [Povolit místní ochranu heslem služby Azure AD](howto-password-ban-bad-on-premises-deploy.md).
