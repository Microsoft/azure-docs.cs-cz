---
title: Nejčastější dotazy ohledně Azure prostorových kotvy | Dokumentace Microsoftu
description: Azure prostorových kotvy je spravovaná Cloudová služba a pro vývojáře platformu, která umožňuje různá zařízení, více uživatelů, smíšené reality prostředí napříč HoloLens a zařízení s Androidem a iOS. Tyto nejčastější dotazy k otázky týkající se služby z technického hlediska.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: cd004e1eb6a40648c52dd3b5aee0ec38bff8f9b3
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304192"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Nejčastější dotazy k Azure Spatial Anchors

Azure prostorových kotvy je spravovaná Cloudová služba a platformu pro vývojáře, které se dají více uživatelů, prostorově vědět smíšené reality prostředí napříč HoloLens, iOS a androidem.

Další informace najdete v tématu [přehled Azure prostorových kotvy](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Nejčastější dotazy k Azure prostorových kotvy produktu

**Otázka: Zařízení, která podporuje prostorových kotvy Azure?**

**Odpověď:** Azure prostorových kotvy umožňuje vývojářům vytvářet aplikace na HoloLens, na zařízeních s iOS s podporou Arkitem a v systému Android podporují zařízení s ARCore; pro zařízení s iOS a Android to zahrnuje telefonech a tabletech.

**Otázka: Budu muset být připojená ke cloudu použití prostorových kotvy Azure?**

**Odpověď:** Azure prostorových kotvy aktuálně vyžaduje síťové připojení k Internetu. Vítáme vaše komentáře na naše [web pro zasílání názorů](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**Otázka: Jaké jsou požadavky na připojení k Azure prostorových ukotvení?**

**Odpověď:** Azure prostorových kotvy pracuje s Wi-Fi a mobilního širokopásmového připojení.

**Otázka: Jak přesně můžete vyhledat prostorové kotev vztahů Azure kotvy?**

**Odpověď:** Řada faktorů vliv na přesnost umístění ukotvení – osvětlení, objektů v prostředí a dokonce i na plochu, na kterém je umístí ukotvení. Určit, pokud přesnost bude vyhovovat vašim potřebám, zkuste kotvy v prostředí zástupce, jež je používat. Pokud narazíte na prostředí, kde není přesnost splnit požadavky, přečtěte si téma [protokolování a Diagnostika v Azure prostorových kotvy](./concepts/logging-diagnostics.md).

**Otázka: Jak dlouho trvá vytvoření a vyhledejte kotvy?**

**Odpověď:** Čas potřebný k vytvoření a vyhledejte kotvy je závislá na mnoha faktorech – připojení k síti, zařízení zpracování a zatížení a konkrétní prostředí. Máme zákazníky, kteří vytvářejí aplikace v mnoha oborech, včetně výroby, maloobchodní prodej a hraní her označující, že tato služba umožňuje skvělé uživatelské prostředí pro jejich scénáře.

## <a name="privacy-faq"></a>Nejčastější dotazy týkající se ochrany osobních údajů

**Otázka: Když aplikaci umístí prostorových ukotvení někde všechny aplikace mají k němu přístup?**

**Odpověď:** Účet Azure jsou izolované ukotvení. Jenom aplikace, ke kterým udělíte přístup k vašemu účtu budou mít přístup k ukotvení v rámci účtu.

**Otázka: Jaké informace o prostředí je přenést a uložené ve službě při použití prostorových kotev vztahů Azure? Jsou pořizovat snímky prostředí přenášet a uložené?**

**A**: Při vytváření nebo vyhledání ukotvení, pořizovat snímky prostředí se zpracovávají na zařízení do odvozené formátu. Tento formát odvozené je předána a uložené ve službě.

Zajištění transparentnosti níže je snímek prostředí a odvozené zhuštěné bodu cloudu. Bod cloudu zobrazí geometrické reprezentace prostředí, které má přenáší a ukládají ve službě. Pro každý bod v cloudu zhuštěné bodu jsme přenášet a ukládat hodnoty hash vizuální vlastnosti tohoto bodu. Hodnota hash je odvozen z, ale neobsahuje, všechna data pixelů.

Azure prostorových ukotvení odpovídá [podmínky smlouvy služby Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)a [prohlášení o ochraně osobních údajů Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Prostředí a jeho odvozené zhuštěné bodu cloudu](./media/sparse-point-cloud.png)
*obrázek 1: Prostředí a jeho odvozené zhuštěné bodu cloudu*


**Otázka: Existuje způsob, jak můžu poslat diagnostické informace do společnosti Microsoft?**

**A**: Ano. Azure prostorových kotvy má diagnostickém režimu, který vývojářům můžete nastavit přihlašují přes rozhraní API Azure prostorových ukotvení. To je užitečné, například pokud narazíte na prostředí, kde nelze vytvořit a vyhledejte kotvy předvídatelným způsobem. Můžeme požádat, pokud můžete odeslat diagnostické sestavy obsahující informace, které budeme moct odladit. Další informace najdete v části [protokolování a Diagnostika v Azure prostorových kotvy](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Dostupnost a ceny nejčastější dotazy

**Otázka: Poskytuje smlouvu SLA?**

**Odpověď:** Jak se standardní pro služby Azure, cílem větší než 99,9 % dostupnosti. Všimněte si, že kotvy prostorových Azure je momentálně ve verzi Preview a jako takový [doplňkové podmínky verze Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) použít.

**Otázka: Můžete publikovat Moje aplikace s využitím Azure prostorových kotvy do obchodu s aplikacemi? Můžete použít Azure prostorových ukotvení pro důležité produkční scénáře?**

**Odpověď:** Azure prostorových kotvy je aktuálně ve verzi Preview a během této doby zveme, můžete k vývoji aplikací, [poskytnout zpětnou vazbu](https://feedback.azure.com/forums/919252-azure-spatial-anchors) o produktu a plán pro nasazení v produkčním prostředí.

Datum obecné dostupnosti (GA) bude brzy oznámena.

**Otázka: Máte splněné žádné omezení?**
 
**A**: Ano, máme limitů omezování.  Neočekáváme, že je pro vývoj a testování aplikací typické dosáhnete. Pro nasazení v produkčním prostředí jsou připravené pro podporu požadavků na vysoce škálovatelné našich zákazníků. [Kontaktujte nás](mailto:azuremrs@microsoft.com) fattica. V této fázi ve verzi Preview jsme dosud nepublikovali naše ovládání datových vrstev a cenové struktury, ale Očekáváme, že k tomu brzy.

**Otázka: V jaké oblasti je prostorových kotvy Azure dostupný?**

**Odpověď:** Můžete vytvořit účet Azure prostorových kotvy dnes v oblasti Azure – Východ USA 2. To znamená, že oba výpočty a úložiště, které běží na ní takové tuto službu jsou v této oblasti. Ale nutné dodat, neexistují žádná omezení toho, kde se nachází vaši klienti. V budoucnu ještě rozšíříme regionální dostupnosti služby pro všechny primární oblasti Azure.

**Otázka: Je služba Azure Spatial Anchors zpoplatněná? Jste někdy účtovat?**

**Odpověď:** Můžete najít podrobnosti o cenách služby ve verzi Preview na naše [stránce s cenami](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Nejčastější technické dotazy

**Otázka: Jak funguje Azure prostorových kotvy?**

**Odpověď:** Azure prostorových kotvy závisí na smíšené reality / rozšířených analýzách realitu. Tyto snímače vnímat prostředí s kamery a sledovat zařízení v 6stupňů sady volnosti (6DoF) prochází přes místo.

Zadaný sledování 6DoF jako stavební blok, prostorových kotvy Azure umožňuje určit určitých bodů zájmu o skutečné prostředí jako "ukotvení" body. Ukotvení může například použít k vykreslení obsahu na určitém místě v reálném světě.

Při vytváření ukotvení Klientská sada SDK zaznamenává informace o prostředí kolem tohoto bodu a odesílá je do této služby. Pokud jiné zařízení hledá ukotvení v tomto prostoru stejné, podobně jako data odesílá do této služby. Tato data je hledána dat prostředí dříve uložena. Pozice ukotvení vzhledem k zařízení se pak odešle zpět pro použití v aplikaci.

**Otázka: Jak prostorových kotvy Azure integrovat s Arkitem a ARCore v Iosu a Androidu?**

**Odpověď:** Azure prostorových kotvy využívá nativní sledování možnosti Arkitem a ARCore. Kromě toho naše sady SDK pro iOS a Android nabízejí funkcí, jako je uchování kotvy spravované cloudové služby a umožňuje vaší aplikace k vyhledání těchto kotvy znovu jednoduše připojením ke službě.

**Otázka: Jak Azure prostorových kotvy integrovat s HoloLens?**

**Odpověď:** Azure prostorových kotvy využívá nativní sledování možnosti v HoloLens. Poskytujeme pro sestavování aplikací na HoloLens sadu SDK Azure prostorových ukotvení. Sada SDK se integruje s nativní funkce HoloLens a nabízí další možnosti. Mezi tyto možnosti patří vývojářům aplikací k uchování kotvy spravované cloudové služby a umožňuje vaší aplikace k vyhledání těchto kotvy znovu připojením ke službě.

**Otázka: Jaké platformy a jazyky podporuje prostorových kotvy Azure?**

**Odpověď:** Vývojáři mohou vytvářet aplikace s Azure prostorových ukotvení pomocí známých nástrojů a architektur pro jejich zařízení:

- Unity HoloLens, iOS a Android
- Kód SWIFT a Objective-C v iOS
- Java SDK nebo sady Android NDK v Androidu
- C++/WinRT on HoloLens

Začínáme s [vývoj zde](index.yml).

**Otázka: Funguje s Unreal?**

**Odpověď:** Očekáváme, že mají podporu pro Unreal brzy.

**Otázka: Funguje se Xamarinem?**

**Odpověď:** Ano. Protože neposkytujeme sada Xamarin SDK, Očekáváme, že vývojáři mohou použít prostorová kotvy Azure ve svých aplikacích Xamarin díky integraci s Azure API prostorových kotvy vztahů.
