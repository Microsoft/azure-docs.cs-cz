---
title: Aktualizace stávající nabídky pro Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje, jak aktualizovat stávající nabídky Azure Marketplace pomocí portálu partnerů cloudu.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: bfe2b0c70c8b912f6489ed461f5bcf6f233ed60d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809247"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Aktualizace stávající nabídky pro Azure Marketplace
==============================================

Existují různé typy aktualizací, které můžete provést u vaší nabídky, jakmile je pryč za provozu.   [Portál partnerů cloudu](https://cloudpartner.azure.com/) má několik funkcí, které vám pomohou při úpravách nabídku zkontrolujte změny jsou správné, včetně:

-  Přidává se nová verze image virtuálního počítače (VM) na existující skladové položky
-  Změna oblasti, které skladová jednotka je k dispozici v
-  Přidání nové SKU
-  Aktualizace metadat marketplace pro SKU a nabídky 
-  Aktualizace ceny na průběžné platby
-  Porovnání funkcí
-  Historie funkcí

Po úpravě nabídku nebo skladovou Položku, musíte publikovat předtím, než se změny vysílat živě.
Tento článek vás provede různé aspekty aktualizace vaší nabídky virtuálních počítačů.


<a name="unpermitted-changes-to-vm-offersku"></a>Nedovolené změny nabídku a skladovou Položku virtuálního počítače
-----------------------------------

Existují některé atributy nabídky virtuálních počítačů nebo skladovou jednotku, kterou nelze změnit, jakmile tato nabídka je za provozu na webu Azure Marketplace.

-  ID vydavatele, nabídky a ID nabídky.
-  ID SKU existující skladové položky.
-  Datový disk počet existujících skladových položek.
-  Model fakturace nebo licencí se změní na existující skladové položky.


<a name="updating-the-vm-image-version-for-a-sku"></a>Aktualizuje se verze image virtuálního počítače pro SKU
---------------------------------------

Image virtuálního počítače pro SKU nabídky může mít aktualizované a přinášejí další funkce, opravy zabezpečení, atd. V takových případech můžete aktualizovat image virtuálního počítače, který odkazuje na skladovou jednotku. Aktualizovat image virtuálního počítače použijte následující kroky. 

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2.  V části **všechny nabídky**, najít nabídky, které chcete aktualizovat.
3.  V části **SKU** formuláře, klikněte na SKU, jehož virtuální počítač image je chcete aktualizovat.
4.  V části **disková verze**, klikněte na **+ nová verze disku** přidat nové image virtuálního počítače.
5.  Zadejte nové Image virtuálních počítačů **disková verze**. Verze disku musí podstoupit [sémantickou verzi](http://semver.org/) formátu. Verze by měla mít formát X.Y.Z, kde X, Y a jsou celá čísla. Ujistěte se, že nová verze, které poskytnete je větší než předchozí verze, jinak nebude na webu Azure portal zobrazí nové číslo verze nebo znovu publikovat na Azure marketplace.
6.  Pro **URL virtuálního pevného disku operačního systému**, zadejte sdílený přístupový podpis (SAS) identifikátor URI pro operační systém virtuálního pevného disku. Všimněte si, že nelze změnit počet datových disků mezi různými verzemi SKU. Pokud předchozí verze umožňovaly datové disky, které jsou nakonfigurované, tato nová verze musí mít také datové disky nakonfigurovaný.
7.  Klikněte na **publikovat** aktivovala pracovního postupu publikovat vaši novou verzi virtuálního počítače k publikování na webu Azure marketplace a webu Azure portal.


<a name="changing-regions-a-sku-is-available-in"></a>Změna oblasti SKU je k dispozici v
--------------------------------------

V průběhu času můžete zpřístupnit vaše nabídka/SKU v dalších oblastech.
Alternativně můžete chtít zastavit podporu nabídka/SKU v dané oblasti.
Tyto změny implementovat, použijte následující postup.

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2.  V části **všechny nabídky** najít nabídky, které chcete aktualizovat.
3.  V části **SKU** formulář a potom klikněte na SKU, jejichž dostupnost v oblastech chcete aktualizovat.
4.  Klikněte na **vybrat země** tlačítko **země/oblast dostupnosti** pole.
5.  Ve skupině dostupnosti oblast rozbalovací přidání nebo odebrání oblastí, které chcete pro tuto SKU.
6.  Klikněte na **publikovat** aktivovala pracovního postupu publikovat k aktualizaci vašeho dostupnosti oblast skladové položky.

Pokud SKU je k dispozici v nové oblasti, budete mít možnost určit ceny pro tuto konkrétní oblast prostřednictvím **exportovat Data ceny** funkce. Pokud přidáváte zpět oblasti, který byl dříve k dispozici před, nebude možné aktualizovat jeho ceny od o změně v cenách nejsou povolené.


<a name="adding-a-new-sku"></a>Přidání nových SKU
----------------

Chcete-li zpřístupnit novou skladovou Položku pro váš stávající nabídky, postupujte následujících kroků.

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2.  V části **všechny nabídky** najít nabídky, které chcete aktualizovat.
3.  V části **SKU** formulář, clik na **přidat novou skladovou Položku** a zadejte **SKU ID** v místní nabídce.
4.  Postupujte podle zbývajících kroky popsané v [publikování virtuálního počítače na webu Azure Marketplace](./cloud-partner-portal-publish-virtual-machine.md).
5.  Klikněte na **publikovat** aktivovala publikovat pracovní postup má vysílat živě nové skladové jednotky.


<a name="updating-offer-marketplace-metadata"></a>Aktualizuje se nabídka marketplace metadat.
------------------------------------

Můžete mít scénáře, které je potřeba aktualizovat marketplace metadata přidružená k vaší nabídky, jako je aktualizace vašeho loga společnosti atd. Pomocí následujících kroků pro aktualizaci metadat vaší nabídky.

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2.  V části **všechny nabídky** najít nabídky, které chcete aktualizovat.
3.  Přejít **Marketplace** formuláře a postupujte podle zde uvedených pokynů žádné změny.
4.  Klikněte na **publikovat** aktivovala pracovního postupu publikovat vaše změny budou vysílat živě.


<a name="updating-pricing-on-published-offers"></a>Aktualizují se cenové na publikované nabídky
------------------------------------

Po publikování vaší nabídky s průběžnými platbami, cena existující skladové položky nelze zvýšit, ale můžete vytvořit novou skladovou Položku v rámci stejné nabídky, odstranit staré skladové položky a pak znovu publikovat vaši nabídku. Jsme také umožňují snížit ceny na již publikované nabídky. Ke snížení ceny nabídky:

1.  Klikněte na SKU, pro kterou chcete snížit ceny.
2.  Pokud jste nastavili informace o cenách v 1 × 1 grafické uživatelské rozhraní, můžete změnit ceny přímo v uživatelském rozhraní. Pokud nastavíte ceny prostřednictvím importu/exportu tabulky, může pouze snížit ceny prostřednictvím funkce importu a exportu.
3.  Klikněte na **Uložit**.
4.  Nové publikování vaší nabídky a přechod na živý přenos.

Ceny budou viditelné pro nové zákazníky, jakmile je zcela za provozu na webu a všem novým zákazníkům platit nové nižší ceny.

Stávající zákazníci snížení cen se projeví zpětně na začátku fakturačního cyklu, během které snížení cen začal platit.
Pokud mají byl již účtuje cyklu, během které došlo k cenu, dostanou při jejich příštím účtovacím období pro snížení ceny refundaci.


<a name="simplified-currency-pricing"></a>Ceny zjednodušené měny
---------------------------

Od září 2018 se nazývá obsahují nový oddíl **zjednodušená ceny měny** staly viditelnými pro vás. Microsoft je zjednodušení obchodních Azure Marketplace povolením více předvídatelné ceny a kolekce od svých zákazníků po celém světě. Toto zjednodušení bude dosaženo snížení počtu měny, ve kterých jsme vašim zákazníkům faktury.

Nový oddíl bude trvat ceny v nových měn. Jakmile všichni zákazníci úspěšně migrováni na nových měn vyrovnání, původní části s cenami se vyřadí z provozu a bude i nadále pouze část "Zjednodušená měny ceny".

Budete mít až do 1. listopadu 2018 nastavit nové ceny pro oblasti, ve které měně vyrovnání se mění. Nebudete tak zároveň rozšiřujete ceny pro oblasti, ve které není změněn vyrovnání měny. Tady jsou oblasti, ve které se mění měně:

| Země                  | ISO2 kódu | Zjednodušené fakturační měna |
|--------------------------|-----------|-----------------------------|
| **Alžírsko**              | DZ        | USD                         |
| **Argentina**            | AR        | USD                         |
| **Bahrajn**              | BH        | USD                         |
| **Bělorusko**              | PODLE        | USD                         |
| **Brazílie**               | BRAZÍLIE        | BRL                         |
| **Bulharsko**             | BG        | EUR                         |
| **Chile**                | CL        | USD                         |
| **Kolumbie**             | CO        | USD                         |
| **Kostarika**           | ZNAK CR        | USD                         |
| **Chorvatsko**              | HR        | EUR                         |
| **Česká republika**       | CZ        | EUR                         |
| **Egypt**                | TŘEBA        | USD                         |
| **Guatemala**            | GT        | USD                         |
| **Hongkong**            | HK        | USD                         |
| **Maďarsko**              | HU        | EUR                         |
| **Island**              | JE        | EUR                         |
| **Indonésie**            | ID        | USD                         |
| **Izrael**               | IL        | USD                         |
| **Jordánsko**               | JO        | USD                         |
| **Kazachstán**           | KZ        | USD                         |
| **Keňa**                | KE        | USD                         |
| **Kuvajt**               | KW        | USD                         |
| **Lichtenštejnsko**        | LI        | EUR                         |
| **Makedonie (bývalá republika Jugoslávie)**     | MK        | USD                         |
| **Malajsie**             | MOJE        | USD                         |
| **Mexiko**               | MX        | USD                         |
| **Černá Hora**           | ME        | USD                         |
| **Maroko**              | MA        | USD                         |
| **Nigérie**              | NG        | USD                         |
| **Omán**                 | OBJEKTOVÝ MODEL        | USD                         |
| **Pákistán**             | PK        | USD                         |
| **Paraguay**             | PY        | USD                         |
| **Peru**                 | PE        | USD                         |
| **Filipíny**          | PH        | USD                         |
| **Polsko\***             | PL        | EUR                         |
| **Katar**                | KONTROLA KVALITY        | USD                         |
| **Rumunsko**              | RO        | EUR                         |
| **Saúdská Arábie**         | SA        | USD                         |
| **Srbsko**               | RS        | USD                         |
| **Singapur**            | ZABEZPEČENÍ        | USD                         |
| **Jižní Afrika**         | ZA        | USD                         |
| **Thajsko**             | TH        | USD                         |
| **Trinidad a Tobago**  | TT        | USD                         |
| **Tunisko**              | TN        | USD                         |
| **Turecko**               | TR        | USD                         |
| **Ukrajina**              | UŽIVATELSKÝ AGENT        | USD                         |
| **Spojené arabské emiráty** | AE        | USD                         |
| **Uruguay**              | UY        | USD                         |
|  |  |  |

> [!NOTE]
> Pokud používáte rozhraní API pro publikování vaší nabídky, je možné, uvidíte nový oddíl v rámci nabídky s názvem **JSON**. To by být označena jako `virtualMachinePricingV2` nebo `monthlyPricingV2`, v závislosti na typu nabídky.

Pokud máte po této změně nějaké dotazy, obraťte se na podporu Azure Marketplace.


<a name="compare-feature"></a>Porovnání funkcí
---------------

Pokud provedete změny u již publikované nabídky, můžete použít *porovnání* funkce Auditovat změny, které byly provedeny. Chcete-li využívají porovnání:

1.  V každém okamžiku procesu úprav, můžete kliknout **porovnání** tlačítko pro vaši nabídku.

2.  Zobrazit vedle sebe verze marketingové prostředky a metadata.


<a name="history-of-publishing-actions"></a>Historie publikování akce
-----------------------------

Chcete-li zobrazit všechny historické publikační aktivity, klikněte na **historie** kartu v levém navigačním podokně portálu partnerů cloudu. Tady budou moci zobrazit časovým razítkem akce, která byla přijata po celou dobu životnosti vaší nabídky na webu Azure Marketplace.
