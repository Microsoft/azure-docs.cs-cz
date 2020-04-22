---
title: Soukromé nabídky | Azure Marketplace
description: Privátní nabídky na Azure Marketplace pro vydavatele aplikací a služeb.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: efda9fb66a65f1f2cd40de8bf32a8a3fa99aea7f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681435"
---
# <a name="private-offers"></a>Soukromé nabídky

Privátní nabídky na [webu Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) umožňují vydavatelům vytvářet skumy, které jsou viditelné pouze pro cílové zákazníky.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Odemkněte podnikové obchody se soukromými nabídkami

Podnikoví zákazníci stále více používají online tržiště k hledání, vyzkoušení a nákupu cloudových řešení. Nyní mohou vydavatelé se soukromými nabídkami využívat marketplace k soukromému sdílení přizpůsobených řešení s cílenými zákazníky s funkcemi, které podniky vyžadují:

- *Sjednané ceny* umožňují vydavatelům rozšířit slevy a mimo seznam cen z veřejně dostupných nabídek.
- *Soukromé smluvní podmínky* umožňují vydavatelům přizpůsobit podmínky konkrétnímu zákazníkovi.
- *Specializované konfigurace* umožňují vydavatelům přizpůsobit své virtuální počítače, aplikace Azure a aplikace SaaS nabízejí potřebám jednotlivých zákazníků. Tato možnost také umožňuje majitelům stránek poskytovat náhled přístup k novým funkcím produktu před zahájením více široce všem zákazníkům.

Soukromé nabídky umožňují vydavatelům využívat výhod rozsahu a globální dostupnosti veřejného trhu s flexibilitou a kontrolou potřebnou k vyjednávání a poskytování vlastních obchodů a konfigurací. Tyto funkce společně otevírají dveře silnému podnikovému přijetí cloudových tržišť. Podniky nyní mohou nakupovat a prodávat způsobem, který očekávají a požadují.

Privátní nabídky jsou teď dostupné pro Virtual Machine, Azure Application (implementované jako šablony řešení nebo spravované aplikace) a nabídky Aplikací SaaS. 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>Vytváření soukromých nabídek pomocí slok a plánů

U *nových nebo stávajících nabídek s veřejnými skum nebo plány*mohou vydavatelé snadno vytvářet nové soukromé varianty vytvořením nových skutnic nebo plánů a jejich označením jako soukromých.  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

Soukromé skuanové a plány jsou součástí nabídky a jsou viditelné a lze je zakoupit pouze pro cílové zákazníky. Soukromé skladové položky a plány můžete znovu použít základní bitové kopie nebo nabídnout metadata již publikována pro veřejné skladové položky nebo plán. Tato možnost umožňuje vydavatelům vytvořit více soukromých variant veřejné nabídky, aniž by museli publikovat více verzí stejné základní bitové kopie a nabízet metadata. Pro virtuální počítač a Azure nabízí pouze v případě, že soukromá skladová položka sdílí základní bitovou kopii s veřejnou skladovou položkou, všechny změny základní image nabídky se rozšíří napříč všemi veřejnými a soukromými skladovými objekty s využitím této základní bitové kopie.

U *nových nabídek, které zahrnují pouze soukromé skutnice nebo plány*, mohou vydavatelé vytvářet své nabídky jako jakoukoli jinou nabídku a označovat je jako soukromé. Nabídky, které mají pouze soukromé skum nebo plány, nebudou zjistitelné nebo přístupné prostřednictvím komerčního tržiště Microsoftu nebo [portálu Azure](https://azure.microsoft.com/features/azure-portal/) zákazníky, kteří nejsou přidruženi k nabídce.

## <a name="targeting-customers-with-private-offers"></a>Cílení na zákazníky pomocí soukromých nabídek

U nových i stávajících privátních nabídek mohou vydavatelé cílit na zákazníky pomocí identifikátorů předplatného. Vydavatelé používající virtuální počítač nebo nabídku aplikací Azure můžou omezit dostupnost privátní skladové položky na individuální ID předplatného Azure nebo nahrát CSV s až 20 000 ID předplatného Azure. Při použití soukromé nabídky aplikace SaaS mohou vydavatelé přidružit id klienta, aby omezili dostupnost soukromého plánu, a to buď pomocí ručního nebo csv uploadového přístupu.

Jakmile je nabídka certifikována a publikována, zákazníci mohou být aktualizováni nebo odebráni ze skladové položky nebo plánu během několika minut pomocí funkce Synchronizovat soukromé předplatná. Tato funkce umožňuje vydavatelům rychle a snadno aktualizovat seznam zákazníků, kterým je soukromá skladová položka nebo plán prezentován, aniž by znovu certifikovali nebo publikovali nabídku.

## <a name="deploying-private-offers"></a>Nasazení soukromých nabídek

Privátní nabídky jsou zjistitelné jenom přes [portál Azure](https://azure.microsoft.com/features/azure-portal/) a nezobrazují se na [Microsoft AppSource](https://appsource.microsoft.com/) nebo [Azure Marketplace](https://azuremarketplace.microsoft.com). Další informace o publikování v různých obchodních výlohách na trhu s potravinami najdete v [tématu Určení možnosti publikování](./determine-your-listing-type.md).

Po přihlášení k portálu Azure si zákazníci můžou vybrat navigační prvek Marketplace, aby měli přístup ke svým soukromým nabídkám. Soukromé nabídky se také zobrazí ve výsledcích hledání a lze je nasadit pomocí příkazového řádku a šablon Azure Resource Manageru jako všechny ostatní nabídky.

![[Soukromé nabídky]](./media/marketplace-publishers-guide/private-offer.png)

Ve výsledcích vyhledávání se také zobrazí soukromé nabídky. Jen se podívejte po odznaku "Vojín".

>[!Note]
>Privátní nabídky nejsou podporovány s předplatnými vytvořenými prostřednictvím prodejce programu Zprostředkovatel e-cloudových řešení (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->