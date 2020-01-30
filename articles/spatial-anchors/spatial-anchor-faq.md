---
title: Nejčastější dotazy
description: Nejčastější dotazy týkající se služby prostorových kotev Azure
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 563e2da8eba228636b05db2112739fdead4a4aa3
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844883"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Nejčastější dotazy k Azure Spatial Anchors

Prostorové kotvy Azure je spravovaná cloudová platforma a platforma pro vývojáře, která umožňuje využívat hybridní realitu v rámci zařízení s režimem HoloLens, iOS a Androidu s více uživateli.

Další informace najdete v tématu [Přehled prostorových kotev Azure](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Nejčastější dotazy k produktu prostorových kotev Azure

**Otázka: která zařízení podporují prostorové kotvy Azure?**

**A:** Prostorové kotvy Azure umožňují vývojářům vytvářet aplikace na HoloLens, na zařízeních s iOS s podporou ARKit a na zařízeních s Androidem s podporou ARCore; pro iOS a Android to platí jak pro telefony, tak pro tablety.

**Otázka: musím být připojení ke cloudu, aby bylo možné používat prostorové kotvy Azure?**

**A:** Prostorové kotvy Azure aktuálně vyžadují síťové připojení k Internetu. Vaše komentáře jsme uvítá na našem [webu pro zpětnou vazbu](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**Otázka: Jaké jsou požadavky na připojení pro prostorové kotvy Azure?**

**A:** Prostorové kotvy Azure fungují s připojením Wi-Fi a mobilní širokopásmové připojení.

**Otázka: jak přesně můžou kotvy prostorů Azure najít kotvy?**

**A:** Mnohé faktory ovlivňují přesnost umístění kotev – světelné podmínky, objekty v prostředí a dokonce i plochu, na které je kotva umístěná. Pokud chcete zjistit, jestli přesnost vyhovuje vašim potřebám, zkuste kotvy v prostředích zástupce tam, kde je plánujete použít. Pokud narazíte na prostředí, kde přesnost nevyhovuje vašim potřebám, přečtěte si téma [protokolování a diagnostika v prostorových ukotveních Azure](./concepts/logging-diagnostics.md).

**Otázka: jak dlouho trvá vytváření a hledání kotev?**

**A:** Čas potřebný k vytvoření a vyhledání kotev závisí na mnoha faktorech – na síťovém připojení, na zpracování a zatížení zařízení a na konkrétním prostředí. Máme zákazníky, kteří sestavují aplikace v mnoha odvětvích, včetně výroby, maloobchodu a her, což znamená, že služba umožňuje skvělé uživatelské prostředí pro své scénáře.

## <a name="privacy-faq"></a>Nejčastější dotazy týkající se ochrany osobních údajů

**Otázka: když aplikace umístí prostorovou kotvu, ke které mají přístup všechny aplikace?**

**A:** Kotvy jsou izolované pomocí účtu Azure. Jenom aplikace, kterým udělíte přístup k vašemu účtu, budou mít přístup k kotvám v rámci účtu.

**Otázka: Jaké informace o prostředí se přenáší a ukládají do služby při použití prostorových kotev Azure? Jsou obrázky prostředí přenášeny a uloženy?**

Odpověď **: při**vytváření nebo hledání kotev jsou obrázky prostředí zpracovávány v zařízení do odvozeného formátu. Tento odvozený formát je přenášen do služby a uložen ve službě.

K zajištění transparentnosti níže je obrázek prostředí a odvozeného cloudu řídce používaného bodu. V cloudu Point se zobrazuje geometrická reprezentace prostředí, které se přenáší a ukládá do služby. Pro každý bod v cloudu se zhuštěným bodem odesíláme a ukládáme hodnotu hash vizuálních vlastností tohoto bodu. Hodnota hash je odvozena z, ale neobsahuje data v pixelech.

Prostorové kotvy Azure vyhovují [podmínkám smlouvy o poskytování služeb Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)a [prohlášením o zásadách ochrany osobních údajů společnosti Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![prostředí a jeho odvozený cloudový bod](./media/sparse-point-cloud.png)
*Obrázek 1: prostředí a jeho odvozené cloudové body*


**Otázka: existuje způsob, jak mohu odeslat diagnostické informace společnosti Microsoft?**

**Odpověď:** Ano. Prostorové kotvy Azure mají režim diagnostiky, který můžou vývojáři vybrat k výslovným souhlasit prostřednictvím rozhraní API prostorových kotev Azure. To je užitečné, například pokud narazíte na prostředí, ve kterém nemůžete vytvářet a vyhledávat kotvy prediktivním způsobem. Můžeme se zeptat, jestli můžete odeslat diagnostickou zprávu obsahující informace, které nám pomáhají při ladění. Další informace najdete [v tématu protokolování a diagnostika v prostorových ukotveních Azure](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Nejčastější dotazy týkající se dostupnosti a cen

**Otázka: zadáváte smlouvu SLA?**

**A:** Jako standard pro služby Azure cílíme na dostupnost větší než 99,9%. Všimněte si, že prostorové kotvy Azure jsou momentálně ve verzi Preview a že se používají [Doplňkové podmínky pro verzi Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

**Otázka: Můžu své aplikace publikovat pomocí prostorových kotev Azure do obchodů s aplikacemi? Můžu použít prostorové kotvy Azure pro kritické provozní scénáře?**

**A:** Prostorové kotvy Azure jsou momentálně ve verzi Preview a během této doby vás zve k vývoji aplikací, [poskytování zpětné vazby](https://feedback.azure.com/forums/919252-azure-spatial-anchors) k produktu a naplánování produkčních nasazení.

Data obecné dostupnosti (GA) budou brzy oznámena.

**Otázka: Existují nějaká omezení omezování?**

Odpověď **: Ano**, máme omezení omezování.  Neočekáváme, že získáte typické vývoj a testování aplikací. U produkčních nasazení jsme připraveni podporovat vysoce škálovatelné požadavky našich zákazníků. [Kontaktujte nás, abychom](mailto:azuremrs@microsoft.com) mohli diskutovat. V této fázi Preview jsme ještě nepublikovali naši vrstvu a cenovou strukturu, ale očekáváme, že to uděláte brzo.

**Otázka: v jakých oblastech jsou k dispozici prostorové kotvy Azure?**

**A:** Účet prostorových kotev Azure můžete v současnosti vytvořit v oblasti Azure Východní USA 2. To znamená, že tato služba je v této oblasti výpočetní i úložná. V takovém případě neexistují žádná omezení, kde se nacházejí vaši klienti. V budoucnu budeme rozšiřovat regionální dostupnost služby do všech primárních oblastí Azure.

**Otázka: Chcete účtovat za prostorové kotvy Azure? Budete někdy účtovat?**

**A:** Podrobnosti o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/spatial-anchors/)ve verzi Preview.

## <a name="technical-faqs"></a>Technické Nejčastější dotazy

**Otázka: jak fungují kotvy prostorů Azure?**

**A:** Prostorové kotvy Azure závisí na smíšených sledováních realit a rozšíření realit. Tyto stopy vnímat prostředí s kamerami a sledují zařízení ve 6 až stupních volná (6DoF) při pohybu přes prostor.

Vzhledem k tomu, že sledování 6DoF jako stavební blok, vám prostorové kotvy Azure umožňuje určit určité body zájmu v reálném prostředí jako "kotvicí" body. Můžete například použít kotvu k vykreslení obsahu na určitém místě reálného světa.

Při vytváření kotvy sada SDK klienta zachycuje informace o prostředí kolem daného bodu a přenáší ho do služby. Pokud jiné zařízení vyhledá kotvu ve stejném prostoru, podobná data se přenáší do služby. Tato data se shodují s dříve uloženými daty prostředí. Pozice kotvy relativní vzhledem k zařízení se pak pošle zpátky pro použití v aplikaci.

**Otázka: jak se v systémech iOS a Android integrují prostorové kotvy Azure s ARKit a ARCore?**

**A:** Prostorové ukotvení Azure využívá nativní možnosti sledování ARKit a ARCore. Kromě toho naše sady SDK pro iOS a Android nabízejí funkce, jako jsou trvalé kotvy ve spravované cloudové službě, a umožňují vašim aplikacím, aby tyto kotvy našli znovu, a to jednoduše připojením ke službě.

**Otázka: Jak lze integrovat prostorové kotvy Azure pomocí HoloLens?**

**A:** Prostorové ukotvení Azure využívá nativní možnosti sledování HoloLens. Poskytujeme sadu SDK prostorových kotev pro Azure pro vytváření aplikací na HoloLens. Sada SDK se integruje s nativními možnostmi HoloLens a poskytuje další funkce. Mezi tyto možnosti patří povolení vývojářů aplikací, aby zachovali kotvy ve spravované cloudové službě a aby vaše aplikace dokázaly tyto kotvy vyhledat znovu připojením ke službě.

**Otázka: které platformy a jazyky podporují prostorové kotvy Azure?**

**A:** Vývojáři mohou vytvářet aplikace pomocí prostorových kotev Azure pomocí známých nástrojů a architektur pro zařízení:

- Unity napříč HoloLens, iOS a Androidem
- Xamarin v iOS a Androidu
- SWIFT nebo objektivní – C v iOS
- Java nebo Android NDK v Androidu
- C++/WinRT na HoloLens

Začněte s [vývojem tady](index.yml).

**Otázka: funguje s Unreal?**

**A:** V budoucnu se bude brát v úvahu podpora pro Unreal.

**Otázka: jaké porty a protokoly používá prostorové kotvy Azure?**

**A:** Prostorové kotvy Azure komunikují přes port TCP 443 pomocí šifrovaného protokolu. Pro ověřování používá [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/), který komunikuje pomocí protokolu HTTPS přes port 443.
