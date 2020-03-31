---
title: Nejčastější dotazy
description: Časté otázky týkající se služby Azure Spatial Anchors.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 563e2da8eba228636b05db2112739fdead4a4aa3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76844883"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Nejčastější dotazy týkající se prostorových ukotvení Azure

Azure Spatial Anchors je spravovaná cloudová služba a vývojářská platforma, která umožňuje prostředí smíšené reality pro více uživatelů, prostorově uvědomit si napříč zařízeními HoloLens, iOS a Android.

Další informace naleznete v [tématu Přehled prostorových kotev Azure](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Nejčastější dotazy k produktu Azure Spatial Anchors

**Otázka: Která zařízení podporují azure prostorové kotvy?**

**A:** Azure Spatial Anchors umožňuje vývojářům vytvářet aplikace na HoloLens, na zařízeních se systémem iOS s podporou ARKitu a na zařízeních s Androidem s podporou ARCore; pro iOS a Android to zahrnuje telefony i tablety.

**Otázka: Musím být připojen ke cloudu, abych mohl používat Azure Spatial Anchors?**

**A:** Azure Spatial Anchors aktuálně vyžaduje připojení k internetu. Uvítáme vaše připomínky na našich [stránkách zpětné vazby](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**Otázka: Jaké jsou požadavky na připojení pro azure prostorové kotvy?**

**A:** Azure Spatial Anchors funguje s Wi-Fi a mobilním širokopásmovým připojením.

**Otázka: Jak přesně může Azure Spatial Anchors lokalizovat kotvy?**

**A:** Přesnost umístění kotev ovlivňuje mnoho faktorů – světelné podmínky, objekty v prostředí a dokonce i povrch, na kterém je kotva umístěna. Chcete-li zjistit, zda přesnost bude splňovat vaše potřeby, zkuste kotvy v prostředích reprezentativní, kde je chcete použít. Pokud narazíte na prostředí, kde přesnost neodpovídá vašim potřebám, přečtěte si informace [o protokolování a diagnostice v prostorových kotvách Azure](./concepts/logging-diagnostics.md).

**Otázka: Jak dlouho trvá vytvoření a lokalizaci kotev?**

**A:** Čas potřebný k vytvoření a lokalizaci kotev závisí na mnoha faktorech – síťové připojení, zpracování a načtení zařízení a konkrétní prostředí. Máme zákazníky, kteří budují aplikace v mnoha průmyslových odvětvích, včetně výroby, maloobchodu a her, což naznačuje, že služba umožňuje skvělé uživatelské prostředí pro jejich scénáře.

## <a name="privacy-faq"></a>Nejčastější dotazy k ochraně osobních údajů

**Otázka: Když moje aplikace umístí prostorovou kotvu někam, mají k ní přístup všechny aplikace?**

**A:** Kotvy jsou izolované podle účtu Azure. Přístup ke kotvám v rámci účtu budou mít pouze aplikace, kterým udělíte přístup ke svému účtu.

**Otázka: Jaké informace o prostředí se přenášejí a ukládají ve službě při použití azure prostorových kotev? Jsou obrázky prostředí přenášeny a ukládány?**

**A**: Při vytváření nebo vyhledání kotev jsou obrázky prostředí zpracovány na zařízení do odvozeného formátu. Tento odvozený formát je přenášen a uložen ve službě.

Chcete-li zajistit průhlednost, níže je obrázek prostředí a odvozeného řídkého mračna bodů. Mračno bodů zobrazuje geometrické znázornění prostředí, které je přenášeno a uloženo ve službě. Pro každý bod v řídkém mračnu bodů přenášíme a ukládáme hash vizuálních vlastností tohoto bodu. Hash je odvozen z, ale neobsahuje žádná data pixelů.

Azure Spatial Anchors dodržuje [podmínky smlouvy o poskytování služeb Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)a Prohlášení o [zásadách ochrany osobních údajů společnosti Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Prostředí a jeho odvozený řídký mračno](./media/sparse-point-cloud.png)
bodů*Obrázek 1: Prostředí a jeho odvozený řídký mračno bodů*


**Otázka: Existuje způsob, jak můžete odeslat diagnostické informace společnosti Microsoft?**

**A:** Ano. Azure Spatial Anchors má diagnostický režim, který vývojáři mohou zvolit, aby se přihlásili prostřednictvím rozhraní API Azure Spatial Anchors. To je užitečné, například pokud narazíte na prostředí, kde není možné vytvořit a vyhledat kotvy předvídatelně. Můžeme se zeptat, zda můžete odeslat diagnostickou zprávu obsahující informace, které nám pomáhají ladit. Další informace najdete [v tématu Protokolování a diagnostika v prostorových kotvách Azure](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Časté dotazy k dostupnosti a stanovení cen

**Otázka: Poskytujete sla?**

**A:** Jako standard pro služby Azure cílíme na dostupnost vyšší než 99,9 %. Všimněte si, že Azure prostorové kotvy je aktuálně ve verzi Preview a jako takové [preview doplňkové podmínky.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

**Otázka: Můžu publikovat své aplikace pomocí Azure Spatial Anchors do obchodů s aplikacemi? Můžu použít Azure Spatial Anchors pro klíčové produkční scénáře?**

**A:** Azure Spatial Anchors je aktuálně ve verzi Preview a během této doby vás zveme k vývoji aplikací, [poskytnutí zpětné vazby](https://feedback.azure.com/forums/919252-azure-spatial-anchors) k produktu a plánování pro vaše produkční nasazení.

Termíny všeobecné dostupnosti (GA) budou oznámeny brzy.

**Otázka: Máte nějaké omezení škrcení na místě?**

**A:** Ano, máme omezení omezení.  Neočekáváme, že budete hit je pro typický vývoj aplikací a testování. Pro produkční nasazení jsme připraveni podporovat požadavky našich zákazníků ve velkém měřítku. [Kontaktujte nás](mailto:azuremrs@microsoft.com) a proberte to. Během této fáze náhledu jsme ještě nezveřejnili naši úroveňovací a cenovou strukturu, ale očekáváme, že tak učiníme brzy.

**Otázka: V jakých oblastech jsou azure prostorové kotvy k dispozici?**

**A:** Účet Azure Spatial Anchors můžete vytvořit ještě dnes v oblasti Azure East US 2. To znamená, že výpočetní prostředky i úložiště pohánějící tuto službu jsou v této oblasti. To znamená, že neexistují žádná omezení, kde se nacházejí vaši klienti. V budoucnu rozšíříme místní dostupnost služby do všech primárních oblastí Azure.

**Otázka: Účtujete za prostorové kotvy Azure? Budete někdy účtovat?**

**A:** Podrobnosti o cenách najdete během náhledu na naší [stránce s cenami](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Nejčastější technické otázky

**Otázka: Jak azure prostorové kotvy práce?**

**A:** Azure Spatial Anchors závisí na sledování smíšené reality / rozšířené reality. Tyto trackery vnímají prostředí s kamerami a sledovat zařízení v 6-stupňů-of-freedom (6DoF), jak se pohybuje v prostoru.

Vzhledem k tomu, 6DoF tracker jako stavební blok, Azure prostorové kotvy umožňuje určit určité body zájmu ve vašem reálném prostředí jako "kotevní" body. Můžete například použít kotvu k vykreslení obsahu na určitém místě v reálném světě.

Při vytváření kotvy klientská sada SDK zaznamená informace o prostředí kolem tohoto bodu a přenáší je do služby. Pokud jiné zařízení hledá kotvu ve stejném prostoru, podobná data se přenášejí do služby. Tato data jsou porovnána s dříve uloženými daty prostředí. Pozice kotvy vzhledem k zařízení je pak odeslána zpět pro použití v aplikaci.

**Otázka: Jak se azure prostorové kotvy integrují s ARKit a ARCore v iOS a Androidu?**

**A:** Azure Spatial Anchors využívá nativní možnosti sledování ARKit a ARCore. Kromě toho naše sady SDK pro iOS a Android nabízejí funkce, jako jsou trvalé kotvy ve spravované cloudové službě a umožňují vašim aplikacím znovu vyhledat tyto kotvy pouhým připojením ke službě.

**Otázka: Jak se azure prostorové kotvy integrují s HoloLens?**

**A:** Azure Spatial Anchors využívá nativní možnosti sledování HoloLens. Poskytujeme Azure Spatial Anchors SDK pro vytváření aplikací na HoloLens. Sada SDK se integruje s nativními funkcemi HoloLens a poskytuje další funkce. Mezi tyto funkce patří povolení vývojářům aplikací zachovat kotvy ve spravované cloudové službě a umožnit aplikacím znovu vyhledat tyto kotvy připojením ke službě.

**Otázka: Které platformy a jazyky podporují azure prostorové kotvy?**

**A:** Vývojáři můžou vytvářet aplikace s azure spatial anchors pomocí známých nástrojů a architektur pro své zařízení:

- Jednota napříč HoloLens, iOS a Androidem
- Xamarin na iOS a Android
- Swift nebo Objective-C na iOS
- Java nebo Android NDK na Androidu
- C++/WinRT na HoloLens

Začínáme s [vývojem zde](index.yml).

**Otázka: Funguje to s Unreal?**

**A:** Podpora unreal bude v budoucnu zvážena.

**Otázka: Jaké porty a protokoly azure prostorové kotvy používají?**

**A:** Azure Spatial Anchors komunikuje přes port TCP 443 pomocí šifrovaného protokolu. Pro ověřování používá [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/), který komunikuje pomocí protokolu HTTPS přes port 443.
