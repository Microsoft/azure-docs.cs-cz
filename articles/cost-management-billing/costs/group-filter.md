---
title: Možnosti filtrování a seskupení ve službě Azure Cost Management
description: Tento článek vysvětluje, jak používat možnosti filtrování a seskupení ve službě Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 06/08/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: ac9828ca61009eb3ee39412169b2b454b9ecbd00
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131866"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>Možnosti filtrování a seskupení v analýze nákladů

Analýza nákladů nabízí řadu možností seskupení a filtrování. Tento článek vám pomůže pochopit, kdy je používat.

Pokud se chcete podívat na video o možnostech seskupení a filtrování, najdete ho tady: [Generování sestav služby Cost Management podle dimenzí a značek](https://www.youtube.com/watch?v=2Vx7V17zbmk). Další videa najdete v [kanálu služby Cost Management na YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>Vlastnosti filtrů a skupin

V následující tabulce jsou uvedené některé nejběžnější možnosti seskupování a filtrování, které jsou k dispozici pro analýzu nákladů, a situace vhodné k jejich použití.

| Vlastnost | Kdy je použít | Poznámky |
| --- | --- | --- |
| **Zóny dostupnosti** | Chcete rozdělit náklady na služby AWS podle zóny dostupnosti. | Dá se použít jenom pro obory služby AWS a skupiny pro správu. Údaje o Azure nezahrnují zónu dostupnosti a budou se zobrazovat jako **Nepoužitelné**. |
| **Fakturační období** | Chcete rozdělit náklady na průběžně placené služby podle měsíce, ve kterém byly nebo budou fakturované. | Pomocí možnosti **Fakturační období** můžete získat přesný rozpis fakturovaných poplatků za průběžně placené služby. Pokud filtrujte podle vlastního rozsahu dat, zahrňte 2 dny před fakturačním obdobím a po něm. Při omezení na přesná data fakturačního období by se částky nemusely shodovat s fakturou. Takto si můžete zobrazit náklady ze všech faktur ve fakturačním období. K vyfiltrování konkrétní faktury použijte **ID faktury**. Platí jenom pro předplatná s průběžnými platbami, protože účty EA a MCA se fakturují podle kalendářních měsíců. Účty EA nebo MCA můžou stejného cíle dosáhnout tak, že jako datum vyberou kalendářní měsíc nebo zvolí měsíční úroveň podrobností. |
| **Typ poplatku** | Chcete rozdělit náklady na využití, nákupy, refundace a nevyužité rezervace. | Nákupy rezervací a refundace jsou k dispozici jenom při použití skutečných nákladů (ne při použití amortizačních nákladů). Náklady na nevyužité rezervace jsou dostupné jenom v zobrazení amortizačních nákladů. |
| **Oddělení** | Chcete rozdělit náklady podle oddělení ve smlouvě EA. | Je k dispozici jenom pro účty EA a skupiny pro správu. Předplatná s průběžnými platbami nemají oddělení a budou se zobrazovat jako **Nepoužitelné** nebo **Nepřiřazeno**. |
| **Registrační účet** | Chcete rozdělit náklady podle vlastníka účtu EA. | Je k dispozici jenom pro fakturační účty EA, oddělení a skupiny pro správu. Předplatná s průběžnými platbami nemají registrační účty EA a budou se zobrazovat jako **Nepoužitelné** nebo **Nepřiřazeno**. |
| **Frekvence** | Chcete rozdělit náklady založené na využití, jednorázové náklady a opakované náklady. | |
| **ID faktury** | Chcete rozdělit náklady podle faktury, ve které byly fakturované. | Nefakturované poplatky ještě nemají ID faktury a náklady u účtů EA neobsahují podrobnosti o faktuře. Tyto náklady se tedy budou zobrazovat jako **Nepoužitelné**.  |
| **Měřič** | Chcete rozdělit náklady podle měřiče využití. | Nákupy a využití Marketplace se zobrazí jako **Nepoužitelné**. Nákupy můžete identifikovat pomocí možnosti **Typ poplatku** a poplatky za Marketplace pomocí možnosti **Typ vydavatele**. |
| **Operace** | Chcete rozdělit náklady na služby AWS podle operace. | Dá se použít jenom pro obory služby AWS a skupiny pro správu. Údaje o Azure nezahrnují operaci a budou se zobrazovat jako **Nepoužitelné**. U nich použijte možnost **Měřič**. |
| **Cenový model** | Chcete rozdělit náklady na vyžádání, rezervace nebo spotové využití. | Nákupy se zobrazují jako **Na vyžádání**. Pokud se vám zobrazí údaj **Nepoužitelné**, můžete rezervace nebo využití na vyžádání určit seskupením podle **Rezervace**. K identifikaci nákupů můžete využít **Typ poplatku**.
| **Poskytovatel** | Chcete rozdělit náklady za služby AWS a Azure. | Je k dispozici jenom pro skupiny pro správu. |
| **Typ vydavatele** | Chcete rozdělit náklady za služby AWS, Azure a Marketplace. |  |
| **Rezervace** | Chcete rozdělit náklady podle rezervace. | Veškeré nákupy nebo využití, které nejsou přidružené k rezervaci, se zobrazí jako **Nepoužitelné**. Nákupy přes Azure, AWS nebo Marketplace můžete identifikovat seskupením podle **typu vydavatele**. |
| **Prostředek** | Chcete rozdělit náklady podle prostředku. | Nákupy se zobrazují jako **Nepoužitelné**, protože se aplikují na úrovni fakturačního účtu EA/PAYG nebo na úrovni fakturačního profilu MCA a nejsou přidružené ke konkrétnímu prostředku. Nákupy přes Azure, AWS nebo Marketplace můžete identifikovat seskupením podle **typu vydavatele**. |
| **Skupina prostředků** | Chcete rozdělit náklady podle skupiny prostředků. | Nákupy, prostředky tenanta nepřidružené k předplatným, prostředky předplatných nenasazené do skupiny prostředků a klasické prostředky nemají skupinu prostředků a budou se zobrazovat jako **ostatní**, **klasické služby**, **$system** nebo **Nepoužitelné**. |
| **Typ prostředku** | Chcete rozdělit náklady podle typu prostředku. | Nákupy a klasické služby nemají typ prostředku Azure Resource Manageru a budou se zobrazovat jako **ostatní**, **klasické služby** nebo **Nepoužitelné**. |
| **Umístění prostředku** | Chcete rozdělit náklady podle umístění nebo oblasti. | Nákupy a využití Marketplace se můžou zobrazovat jako **nepřiřazeno**, **neznámé**, **nenamapované** nebo **Nepoužitelné**. |
| **Název služby** nebo **Kategorie měřiče** | Chcete rozdělit náklady podle služby Azure. | Nákupy a využití Marketplace se zobrazí jako **Nepoužitelné** nebo **nepřiřazeno**. |
| **Úroveň služby** nebo **Podkategorie měřiče** | Chcete rozdělit náklady podle dílčí klasifikace měřičů využití Azure. | Nákupy a využití Marketplace se zobrazí jako **Nepoužitelné** nebo **nepřiřazeno**. |
| **Předplatné** | Chcete rozdělit náklady podle předplatného Azure a propojeného účtu AWS. | Nákupy a prostředky tenanta se můžou zobrazit jako **Nepoužitelné**. |
| **Tag** | Chcete rozdělit náklady podle hodnot značky pro určitý klíč značky. | Značky nejsou k dispozici pro nákupy, prostředky tenanta nepřidružené k předplatným, prostředky předplatných nenasazené do skupiny prostředků nebo klasické prostředky. Některé služby nezahrnují značky do údajů o využití. Přečtěte si také další informace o [podpoře značek pro jednotlivé typy prostředků](../../azure-resource-manager/management/tag-support.md). |

Další informace o použitých termínech najdete v článku s [vysvětlením termínů používaných v souboru o využití Azure a poplatcích](../understand/understand-usage.md).

## <a name="next-steps"></a>Další kroky

- [Začínáme s analýzou nákladů](./quick-acm-cost-analysis.md)