---
title: Vytváření nabídek pro Marketplace | Azure Marketplace
description: Vytvářejte nabídky na tržištích Azure a AppSource pomocí portál partnerů cloudu
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pabutler
ms.openlocfilehash: 4aa4e154c99f20963485a98d1a21af4ae2b7a1b1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828099"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Vytváření nabídek Azure Marketplace a AppSource

Jedním z podstatných účelů portál partnerů cloudu je umožnit vydavatelům vytvářet (a publikovat) nabídky na tržištích Microsoft Azure a AppSource.  Tato operace vždy zahájí výběr požadovaného typu nabídky z [nabídky nová](../portal-tour/cpp-new-offer-menu.md)nabídka.  V odpovědi se pro daný typ nabídky zobrazí příslušná **Nová stránka nabídky** .  Například následující obrázek ukazuje výchozí stránku **nové nabídky** pro typ aplikace Azure.

![Nová výchozí stránka nabídky](./media/new-offer-page.png)

V horizontálním řádku nabídek zobrazeném směrem k horní části této stránky jsou k dispozici dva výběry karet: 
- Karta **Editor** – povolí záznam informací a nahrávání prostředků pro novou instanci nabídky.  Tato karta se zobrazí ve výchozím nastavení.
- Karta **stav** – poskytuje stav publikování a uvádí všechny problémy ověřování a kontroly. 

Když vytvoříte nabídku, můžete k zadání informací o této nabídce použít kartu **Editor** . 

## <a name="editing-operations"></a>Operace úprav

Vodorovný panel nástrojů umístěný nad oblastí pro zadávání dat zobrazuje následující tlačítka:

|   Tlačítko    |   Účel                                                          |
|   ------    |  --------                                                          |
| **Uloží**    | Uloží všechny změny nedávných záznamů dat.  Předtím, než přejdete na stránku nebo dojde ke ztrátě změn, je nutné ručně uložit změny. | 
| **Odstraňovat** | Zahodí nedávné změny v datech (od posledního uložení).             |
| **Porovnán** | Porovná stav aktuální nabídky s publikovanou nabídkou.  Povoluje se jenom po úspěšném publikování nabídky.  |
| **Publikování** | Zahájí proces publikování pro tuto nabídku.                       |
| **Odstranění**  | Odstraní tuto nabídku po vytvoření, ale ještě před jejím publikováním. |
|   |   |


## <a name="editing-tabs"></a>Úpravy karet

Při vytváření nabídky zadáte požadovaná a volitelná data na každé kartě nacházející se v levém svislém sloupci stránky **nové nabídky** .  Standardní ovládací prvky uživatelského rozhraní – například textová pole, rozevírací nabídky a zaškrtávací políčka – jsou zobrazeny pro shromažďování dat.  I když konkrétní kolekce úprav karet závisí na typu nabídky, v následující tabulce jsou uvedeny některé běžné karty.

|      Název karty       |   Účel                                                            |
|      --------       |   -------                                                            |
| **Nastavení nabídky**  | Shromažďuje informace o identitě nabídky a vydavatele.                    |
| **SKU**            | Definuje technické a obchodní charakteristiky pro každou verzi vaší nabídky sklad-Keeping Unit (SKU). |
| **Testovací jednotka**      | Pro tyto typy, které podporují tuto volitelnou funkci, definuje ukázku pro vaši nabídku.  Další informace najdete v tématu [co je Test Drive?](../test-drive/what-is-test-drive.md) .  |
| **Marketplace** nebo **prezentace** | Shromažďuje textové řetězce, dokumenty a obrázky použité k vypsání nabídky na webu Marketplace. |
| **Podpora**         | Shromažďuje kontaktní informace pro zákazníky, technickou a online podporu.  |
|  |  |

Obsah karet podobně pojmenovaných se může lišit v různých typech nabídek.  Podrobnosti o těchto kartách jsou uvedeny v části "Vytvoření nabídky" pro každý typ nabídky.


## <a name="next-steps"></a>Další kroky

Po vytvoření a uložení nabídky a před nebo po jejím publikování můžete [zobrazit její stav](./cpp-view-status-offer.md).
