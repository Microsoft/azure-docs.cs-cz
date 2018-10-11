---
title: Co je Project Acoustics?
titlesuffix: Azure Cognitive Services
description: Modul plug-in Project Acoustics pro Unity poskytuje okluzi, ozvěnu a prostorový zvuk pro projekty, které cílí na virtuální realitu i tradiční obrazovky.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 6a3c97ead40cfb24604edac0624e38a9b0713fc0
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901020"
---
# <a name="what-is-project-acoustics"></a>Co je Project Acoustics?
Modul plug-in Project Acoustics pro Unity poskytuje okluzi, ozvěnu a prostorový zvuk pro projekty, které cílí na virtuální realitu i tradiční obrazovky. Poskytuje způsob návrhu herní akustiky, který vrství záměry návrháře nad fyzikální simulaci vln.

## <a name="why-use-acoustics-in-virtual-environments"></a>Proč ve virtuálních prostředích využívat akustiku?
Lidé prostřednictvím audiovizuálních vjemů vnímají svět kolem sebe. Kombinace prostorového zvuku a akustiky umožňuje uživatelům hlouběji se ponořit do virtuálních světů. Zde popsaný akustický nástroj analyzuje virtuální světy a vytváří realistickou simulaci akustiky a podporuje proces návrhu po simulaci. Analýza zahrnuje geometrii i materiály každého povrchu na světě. Simulace zahrnuje parametry, jako je směr příchodu (vytváření portálů), síla ozvěny, doba odeznívání a okluzní a rušivé jevy.

## <a name="how-does-this-approach-to-acoustics-work"></a>Jak tento přístup k akustice funguje?
Systém spoléhá na offline výpočty virtuálního světa, které umožňují provádění složitějších simulací, než kdyby se analýza prováděla za běhu. Offline výpočty generují vyhledávací tabulku akustických parametrů. Návrhář určí pravidla, která se na tyto parametry použijí za běhu. Úpravou těchto pravidel je možné dosáhnout hyperrealistických efektů pro emocionálně vypjaté scény nebo méně realistických scén s více zvuky na pozadí.

## <a name="design-process-comparison"></a>Porovnání procesů návrhu
Modul plug-in Project Acoustics podporuje nový proces návrhu akustiky ve scénách Unity. Tento nový proces návrhu si vysvětlíme na porovnání s jedním z aktuálně oblíbených přístupu k akustice.

### <a name="typical-approach-to-acoustics-today"></a>Dnešní typický přístup k akustice
V rámci dnešního typického přístupu k akustice sbíráte objemy ozvěny:

![Zobrazení návrhu](media/reverbZonesAltSPace2.png)

Pak upravíte parametry pro jednotlivé zóny:

![Zobrazení návrhu](media/TooManyReverbParameters.png)

Nakonec přidáte logiku sledování paprsků, abyste v celé scéně dosáhli správného filtrování okluze a rušení, a logiku hledání cest pro vytváření portálů. Tento kód může zvýšit náklady na provoz. Navíc má problémy s řádným fungováním kolem rohů a v nepravidelně tvarovaných scénách dochází k problémům okolo hran.

### <a name="an-alternative-approach-with-physics-based-design"></a>Alternativní přístup spočívá ve fyzikálním návrhu
V rámci přístupu, který nabízí modul plug-in Project Acoustics pro Unity poskytnete tvar a materiály statické scény. Vzhledem k tomu, že je scéna voxelizovaná a v procesu se nepoužívá sledování paprsků, nemusíte zadávat zjednodušenou ani extrémně hustou akustickou síť. Také není potřeba ve scéně označovat objemy ozvěny. Modul plug-in nahraje scénu do cloudu, kde použije fyzikální simulaci vln. Výsledek se integruje do vašeho projektu jako vyhledávací tabulka, kterou je možné upravit a dosáhnout tak jiných estetických nebo herních zážitků.

![Zobrazení návrhu](media/GearsWithVoxels.jpg)

## <a name="requirements"></a>Požadavky
* Unity 2018.2 nebo novější pro vytváření akustiky a Unity 5.2 nebo novější pro návrh a nasazování zvuků
* 64bitová verze editoru Unity pro Windows
* Předplatné Azure Batch pro vytváření akustiky
* Skriptovací modul runtime Unity musí být nastavený tak, aby odpovídal .NET 4.x.

## <a name="platform-support"></a>Podpora platformy
* Windows Desktop (x86 a AMD64)
* Univerzální platforma Windows (x86, AMD64 a ARM)
* Android (x86 a ARM64)

## <a name="download"></a>Ke stažení
Pokud si chcete vyzkoušet akustický modul plug-in, [tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) se můžete zaregistrovat k verzi Designer Preview.

## <a name="next-steps"></a>Další kroky
* Další informace o [procesu návrhu](design-process.md)
* Začínáme s [integrací akustiky do projektu Unity](getting-started.md)

