---
title: Seznámení s Azure dosah (Preview)
description: Tento článek obsahuje přehled služby Azure dosah, včetně jejích funkcí a potíží, které řeší. Azure dosah umožňuje každému uživateli registrovat, zjišťovat, pochopit a využívat zdroje dat.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: e7ad561cf9ce9983f41fa166eb744c90f126e01e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555947"
---
# <a name="what-is-azure-purview"></a>Co je Azure dosah?

> [!IMPORTANT]
> Služba Azure dosah je aktuálně ve verzi PREVIEW. [Doplňkové podmínky použití pro](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview Microsoft Azure zahrnují další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

Azure dosah je nová cloudová služba pro použití uživateli dat. Azure dosah slouží k centrální správě zásad správného řízení dat napříč vašimi datovými podstatami, které jsou rozložená v cloudu i v Prem prostředích. Tato služba umožňuje obchodním analytikům vyhledat relevantní data pomocí smysluplných obchodních podmínek.

Pomocí uživatelského rozhraní dosah nebo rozhraní Apache Atlas API můžou techničtí uživatelé zobrazovat metadata a vyplňování datových assetů v centrálním katalogu.

Odborníci na danou problematiku, vlastníci dat a správci zabezpečení můžou k datovým assetům přidávat poznámky, aby mohli sdílet své obce znalosti. V závislosti na metadatech a kontrole obsahu to dělají s uživatelským rozhraním nebo se škálováním prostřednictvím automatizované klasifikace a zásad automatického označování.

## <a name="discovery-challenges-for-data-consumers"></a>Problémy zjišťování pro spotřebitele dat

V tradičním zjišťování podnikových zdrojů dat byl v závislosti na obcích znalostech organický proces. Pro společnosti, které chtějí nejvíc hodnot z informačních prostředků, tento přístup představuje mnoho výzev:

* Vzhledem k tomu, že k registraci zdrojů dat není k dispozici žádné centrální umístění, mohou uživatelé neznáte zdroj dat, pokud se k němu nepřijde v rámci jiného procesu.
* Pokud uživatel nezná umístění zdroje dat, nemůže se k datům připojit pomocí klientské aplikace. Možnosti využití dat vyžadují, aby uživatelé znali připojovací řetězec nebo cestu.
* Zamýšlené použití dat je skryto uživatelům, pokud neznají umístění dokumentace ke zdroji dat. Zdroje dat a dokumentace můžou být živě na několika místech a můžete je využívat různými druhy prostředí.
* Pokud mají uživatelé dotazy týkající se informačního prostředku, musí vyhledat odborníka nebo tým odpovědný za příslušná data a zapojit tyto odborníky offline. Mezi daty a odborníky, kteří mají perspektivy používání, neexistuje žádné explicitní spojení.
* Pokud uživatelé nerozumí procesu pro vyžádání přístupu ke zdroji dat, zjištění zdroje dat a jeho dokumentace jim nepomůže získat přístup k datům.

## <a name="discovery-challenges-for-data-producers"></a>Problémy zjišťování pro producenty dat

Přestože se spotřebitelé dat potýkají s těmito dříve popsanými výzvami, uživatelé odpovědní za vytváření a správu informačních prostředků se potýkají s vlastními výzvami:

* Zadávání poznámek ke zdrojům dat s popisnými metadaty je často ztráta času. Klientské aplikace obvykle popisy uložené ve zdroji dat ignorují.
* Vytváření dokumentace pro zdroje dat může být obtížné a jedná se o trvalou zodpovědnost za udržování dokumentace v synchronizaci se zdroji dat. Uživatelé možná nebudou důvěřovat dokumentaci, která je zjištěná jako zastaralá.
* Vytváření a správa dokumentace pro zdroje dat je složitá a časově náročná. O to větší je výzva tuto dokumentaci učinit snadno dostupnou pro každého uživatele, který příslušný zdroj dat používá.
* Omezení přístupu ke zdrojům dat a zajištění, aby spotřebitelé dat věděli, jak požádat o přístup, je neustávající výzva.

Pokud jsou tyto výzvy kombinovány, představují významnou bariéru pro společnosti, které chtějí podněcovat a podporovat používání a porozumění podnikovým datům.

## <a name="discovery-challenges-for-security-administrators"></a>Problémy se zjišťováním pro správce zabezpečení

Uživatelé, kteří zodpovídají za zajištění zabezpečení dat jejich organizace, můžou mít kteroukoli z těchto výzev, jako jsou spotřebitelé dat a výrobci, a také tyto další problémy:

* Data organizace se neustále rozšiřují, ukládají a sdílí se novými směry. Úkol zjišťování, ochrany a řízení citlivých dat je takový, který nikdy nekončí. Chcete mít jistotu, že se obsah vaší organizace sdílí se správnými lidmi, aplikacemi a se správnými oprávněními.
* Porozumění úrovním rizika v datech vaší organizace vyžaduje začnete hloubku na váš obsah, hledání klíčových slov, vzorů RegEx a/nebo citlivých datových typů. Citlivé datové typy můžou zahrnovat čísla kreditních karet, čísla sociálního pojištění nebo čísla bankovních účtů, abyste pomohli pojmenovat několik. Je nutné nepřetržitě monitorovat všechny zdroje dat pro citlivý obsah, protože i nejmenší množství ztráty dat může být pro vaši organizaci zásadní.
* Zajištění, že vaše organizace i nadále dodržuje dodržování zásad zabezpečení společnosti, je náročný úkol při zvětšování a změnách obsahu a při aktualizaci těchto požadavků a zásad pro změnu digitálních realit. Správcům zabezpečení se často provádějí úlohy, protože zabezpečení dat v nejkratší možné době zajišťuje.

## <a name="azure-purview-advantages"></a>Výhody Azure dosah

Azure dosah je navržená tak, aby vyřešila problémy uvedené v předchozích částech a pomohla podnikům získat nejvíc hodnoty z jejich stávajících informačních assetů. Katalog zpřístupňuje zdroje dat uživatelům, kteří data spravují, snadno zjistitelné a srozumitelnější.

Azure dosah poskytuje cloudovou službu, do které můžete registrovat zdroje dat. Během registrace zůstanou data ve svém stávajícím umístění, ale do Azure dosah se přidá kopie svých metadat spolu s odkazem na umístění zdroje dat. Tato metadata jsou také indexována, aby byl každý zdroj dat snadno objevitelný prostřednictvím vyhledávání a aby byl srozumitelný uživatelům, kteří ho objevili.

Po registraci zdroje dat můžete rozšířit jeho metadata. Tato metadata přidá uživatel, který zaregistroval zdroj dat nebo jiného uživatele v podniku. Každý uživatel může přidávat poznámky ke zdroji dat zadáním popisů, značek nebo jiných metadat pro vyžádání přístupu ke zdroji dat. Tato popisná metadata doplňují strukturální metadata, například názvy sloupců a datové typy, které jsou registrovány ze zdroje dat.

Primárním účelem registrace zdrojů dat je zjišťování a porozumění zdrojům, a jejich používání. Podnikoví uživatelé mohou potřebovat data pro business intelligence, vývoj aplikací, datové vědy nebo jiný úkol, ve kterém jsou vyžadována správná data. Využívají možnosti zjišťování katalogu Data Catalog k rychlému vyhledání dat, která odpovídají jejich potřebám, pochopení dat k vyhodnocení jeho vhodnosti pro účel a využití dat otevřením zdroje dat v nástroji podle vlastního výběru.

Současně mohou uživatelé přispívat do katalogu označováním, dokumentováním a zadáváním poznámek ke zdrojům dat, které jsou již zaregistrovány. Mohou také registrovat nové zdroje dat, které jsou následně zjišťovány, chápány a spotřebovány komunitou uživatelů katalogu.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít s Azure dosah, najdete v tématu [Vytvoření účtu Azure dosah](create-catalog-portal.md).
